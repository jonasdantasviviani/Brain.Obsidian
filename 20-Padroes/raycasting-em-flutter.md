---
tipo: padrao
titulo: Raycasting em Flutter - primeira pessoa sem 3D
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/fps]
palavras-chave: [raycasting, wolfenstein, dda, primeira pessoa, fps, grade, zbuffer, billboard, olho de peixe, flame, balistica, headshot, regiao do corpo, altura da bala, coice, bot]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-12
confianca: alta
---

# Raycasting em Flutter
## Resumo
Primeira pessoa em Flutter sai de **raycasting sobre uma grade 2D**, nao de 3D - e o resultado
casa melhor com desenho a mao do que 3D casaria.

## Contexto
Implementado no `counter-ragdoll` ([[RagdollGames]]) em 2026-09-09. Flutter nao tem engine 3D;
Flame e 2D. A saida e a mesma do Wolfenstein 3D de 1992.

## Detalhe

### Como funciona
Cada coluna da tela e um raio lancado numa grade. A parede vira um risco vertical de altura
`alturaDaTela / distancia`. Nao ha poligono, camera 3D nem malha - so a grade.

### Por que casa com rabisco
Raycaster **nao tem textura**: a parede e cor chapada modulada por distancia. Isso e exatamente
como funciona desenho a caneta - hachura no lugar de sombra, e silhueta nas quinas. O que num
jogo realista seria limitacao, aqui e o estilo.

### As quatro pecas
1. **DDA**: caminha de borda de celula em borda, entao o custo e proporcional as paredes no
   caminho, nao a distancia. Roda a 60 fps em celular fraco.
2. **Distancia perpendicular, nao euclidiana**. Euclidiana curva as paredes nas bordas da tela
   (olho de peixe). O truque: passar o **vetor de camera nao normalizado** para o DDA - a
   distancia ja sai perpendicular. O mesmo DDA com vetor normalizado da a euclidiana, que e o
   que a linha de visao do bot quer.
3. **z-buffer por coluna**: guardar a profundidade de cada coluna e o que permite esconder o
   inimigo atras da parede.
4. **Billboard** para inimigos e itens: projeta a posicao com a inversa da matriz
   `[plano | direcao]`, e desenha na coluna resultante se o z-buffer deixar. Vale a pena extrair
   isso numa funcao so (`_projetar`) - bot e item usam exatamente o mesmo teste, mudando so a
   altura em que sao desenhados: o bot a partir do chao para cima, o item **no** chao.

### Olhar para cima e para baixo
Nao se gira a camera - **desloca-se o horizonte** na tela (cisalhamento, o truque do Doom):
```
horizonte = altura/2 + miraVertical * altura * 0.42
```
Tudo o mais (topo e base de parede, base de sprite) ja e calculado a partir do horizonte, entao
sai de graca. **Precisa de limite** (~0.85): passando disso a distorcao de cisalhamento fica
visivel, porque nao ha projecao real, so deslocamento.

### Altura do olho: salto de graca
Com altura de olho `z` variavel:
```
topo = horizonte - alturaNaTela * (1 - z)
base = horizonte + alturaNaTela * z
```
Pular so aumenta `z`. Nao precisa de fisica nenhuma no mundo - o salto e uma variavel na camera.

### O ganho de arquitetura
O raycaster inteiro e **matematica pura sobre a grade**: mora em `domain`, sem Flutter e sem
engine, e e testavel em milissegundos. No counter-ragdoll sao 53 testes cobrindo raio, mapa,
salto, arsenal, bots e acerto de bala - todos rodando em `flutter test`, ao contrario dos testes
de fisica do [[forge2d-e-box2d-v3]], que precisam de aparelho.

### Mapa como desenho em texto
```
#####
#J..#
#.#b#
#####
```
`#` parede, `J` jogador, `b` bot. Da para mexer no cenario sem editor, e o mapa cabe numa
revisao de codigo.

### Material se separa por hachura, nao por cor
Com quatro tipos de parede (alvenaria, prateleira, engradado, movel), a tentacao e dar uma cor a
cada um. Nao: cor e informacao, e vermelho ja significa inimigo. O que separa material num
desenho a caneta e a **densidade da hachura** - prateleira risca fino e junto, engradado risca
grosso e espacado.

### Ragdoll num jogo sem mundo fisico
Raycaster nao tem fisica: a grade e logica, nao corpos. Para o inimigo morrer em ragdoll, o
truque e um **palco a parte** - um unico mundo Forge2D onde cada cadaver ocupa uma **raia**
propria (6 m de distancia entre elas), simulado de verdade e depois **projetado no billboard**
do inimigo.

Um mundo so, e nao um por cadaver: a Box2D v3 permite um numero limitado de mundos simultaneos
e nao os libera sozinha - `physicsWorld.destroy()` e obrigatorio.

Depois de alguns segundos o corpo **vira decalque**: congela-se a pose, destroem-se os corpos e
a raia fica livre. E o mesmo movimento resolvendo duas coisas - o corpo continua na cena e para
de custar.

### Arma na mao em primeira pessoa
Desenhar a arma **de perfil** e o erro natural - e ela parece de vitrine, nao empunhada. Em
primeira pessoa ve-se a arma **por tras**: a face traseira voltada para voce e o cano fugindo
para dentro da tela.

A tecnica e um **trilho em perspectiva**: da traseira da arma (abaixo e a direita do centro)
ate o **ponto de fuga, que e a propria mira**. Cada peca (ferrolho, cano, pente, guarda-mao) e
um bloco com profundidade inicial e final nesse trilho, e encolhe linearmente ate a mira. Como
o ponto de fuga e a mira, **o cano sempre aponta para onde a bala vai** - inclusive quando o
recuo sobe a mira, desde que o ponto de fuga acompanhe.

Cada bloco desenha so o que se ve de tras: silhueta (envoltoria convexa dos 8 cantos), a face
traseira e as arestas de cima. A face da frente aponta para longe e nao aparece. Preencher a
silhueta com papel antes do traco faz a peca da frente esconder a de tras.

Tres ajustes que so aparecem na tela:
- **Altura da traseira ~0,80 da tela.** Mais baixo, as maos caem para fora - e sao elas que
  dizem "voce esta segurando isto".
- **Desenhar o punho** entre a arma e a mao. Sem ele a arma flutua acima dos punhos fechados.
- **Cotovelo medido a partir do pulso**, para baixo e para fora, e a mao desenhada **no mesmo
  ponto** do pulso. Medido a partir da arma, os bracos se abrem na horizontal como uma mesa, e
  mao e antebraco se descolam.

### Captura do mouse (pointer lock) sem quebrar o gatilho
Sem trava, o cursor bate na borda da tela e a camera para de girar - Jonas bateu nisso. Com
trava, o Flutter pode perder o "soltar botao" e o jogo para de atirar - tambem aconteceu.
O desenho que resolve os dois (`counter-ragdoll`, 2026-09-10). No painel de teste, que roda
em iframe sem permissao de trava, foi conferido o caminho sem trava (aviso, primeiro clique
sem tiro, segundo com tiro); a trava de verdade e para conferir no Chrome:
- **todo o mouse vem cru do navegador**, por eventos de **ponteiro** em fase de captura:
  `pointerdown`, `pointerup`, `pointermove` (com `movementX/Y`) e `blur`. **Nao** `mousedown`:
  o Flutter cancela o `pointerdown` e isso suprime os eventos de mouse
  ([[flutter-web-cancela-pointerdown]]). O `Listener` do Flutter ignora mouse quando o cru esta
  ativo - senao dispara dobrado. Toque continua pelo Flutter.
- **primeiro clique so captura**, sem atirar, como no CS.
- **ESC**: o navegador consome o ESC para sair da trava; o `pointerlockchange` avisa e o jogo
  abre o menu. O menu ignora ESC nos primeiros 400 ms - o mesmo ESC pode chegar la e fecha-lo.
- **trava que nao vem**: vale **uma tentativa**. Se no clique seguinte o ponteiro ainda nao
  travou, ele atira e tenta travar de novo. **Nao depender do `pointerlockerror`**: no painel de
  teste (iframe sem permissao) ele veio numa execucao e nao veio em outra. Cuidado com a
  primeira leitura: numa terceira execucao ele "nao vinha" porque o pedido de trava nem era
  feito - o `mousedown` era suprimido ([[flutter-web-cancela-pointerdown]]). Melhor mira
  limitada que gatilho morto.
- ao descartar o jogo, **remover os ouvintes antes de soltar a trava** - senao a propria
  soltura dispara "perdeu a trava" e abre o menu por cima da proxima partida.

### Mira com mouse no navegador
Arrastar para virar a camera conflita com segurar o gatilho. O que funciona: escutar `mousemove`
cru no documento e usar `movementX/Y`, que reportam movimento **com ou sem** botao apertado e
seguem funcionando com o ponteiro travado. `MouseRegion.onHover` do Flutter tambem serve, mas
so um dos dois pode estar ligado - com os dois, o mesmo movimento gira em dobro.

**Cuidado com `late final` preguicoso** para quem so existe pelo efeito colateral do construtor
(registrar listener): basta ninguem ler o campo para ele nunca nascer, e o recurso some sem erro
nenhum. Construir no `onLoad`.

### Bala com altura num mundo 2D
O raycaster e 2D, mas a bala nao precisa ser. A altura sai da **mesma inclinacao que a tela
desenha**, entao a bala vai exatamente para onde a mira aponta:

```
inclinacao = miraVertical * 0.42 + 2 * recuo     // 0.42 = fator do horizonte na tela
z(d)       = alturaDoOlho + inclinacao * d       // 0 chao, 1 teto
```

- **Chao e teto:** inclinacao < 0 corta em `d = -olho / inclinacao`; > 0 em
  `d = (1 - olho) / inclinacao`. O limite da bala e o menor entre isso e a parede.
- **Bot como cilindro com faixas:** projeta no raio (`aoLongoDoRaio`), calcula `z` ali e ve
  a regiao: pernas abaixo do quadril, tronco ate o pescoco, cabeca ate o topo. A cabeca tem
  **raio menor** que o corpo - bala na altura da cabeca que passa ao lado e erro.
- **Uma constante so para a altura do bot** (`Bot.altura`), usada pelo desenho e pela bala;
  as fracoes de quadril e pescoco tambem sao as do desenho. Assim o que a mira cobre na tela e
  o que acerta.
- **Mancha na altura do impacto:** `y = horizonte + (h / d) * (olho - z)`; no chao ou no teto,
  achatar o oval.
- **O coice vem depois do disparo.** Aplicado antes, ate o primeiro tiro sai acima da mira -
  no CS a primeira bala vai onde a mira esta e o coice sobe as seguintes.

### Parede baixa (mureta) num raycaster
Cobertura de peito precisa de tres coisas, e todas saem de **um numero** (altura da mureta
entre o olho em pe e o olho agachado):
1. **Raio:** a mureta nao para o DDA quando se passa uma lista - ela e anotada com a
   distancia e o raio segue ate a parede inteira. Sem a lista, ela para como parede.
2. **Bala:** para na primeira mureta em que `z(d) <= altura`. De pe o tiro reto passa
   raspando; agachado, bate.
3. **Linha de visao:** `porCimaDeMureta` ligado para alvo em pe, desligado para agachado -
   e o que faz o esconderijo existir para a IA.

Para a **IA** usar a mesma cobertura, a regra e curta: mureta perto dele na linha do alvo +
arma sem cadencia = abaixa; pronto para atirar = levanta. E o corpo agachado precisa
**encolher de verdade** (altura e hitbox), senao o bot se esconde da tela mas segue sendo
alvo - esconderijo so visual e pior que nenhum.

No desenho: **depois dos bots** (senao nao esconde ninguem) e com **papel opaco por baixo da
hachura**, porque o preenchimento translucido da parede deixa ver o que esta atras. E lembrar
que mureta **fecha passagem**: rodar a validacao de alcance dos mapas depois de espalhar.

### Mapa em grade: identidade nao vem da planta
Num raycaster sem textura, **seis plantas diferentes parecem o mesmo lugar** se a folha e a
caneta forem as mesmas. Barato e eficaz: cada mapa declara papel (pautado, quadriculado,
milimetrado, liso), matiz do fundo e cor da tinta da alvenaria; o resto do desenho nao muda.

Mapa em texto pede **validacao automatica**: flood fill a partir do nascimento e recusar
borda aberta, nascimento dentro da parede, bot ou item ilhado e chao isolado. Sala lacrada
nao aparece no teste de jogo - aparece meses depois, como "esse item nunca da para pegar".
Planta grande (dezenas de salas) sai melhor **gerada por construcao** (corredores, salas,
portas) do que desenhada celula a celula.

### Precisao pelo movimento e mira dinamica
- `espalhamento = base x (1 + penalidade x ritmo^2) x (agachado 0,6) x (no ar 5)`, com
  `ritmo` = andado no passo / velocidade maxima, suavizado (um quadro parado nao zera tudo).
- A mira da tela abre **do tamanho do cone real**: `vao = espalhamento x (w/2) / tan(fov/2)` -
  mesma projecao do raycaster, entao o que a mira mostra e onde a bala pode cair.
- Agachar e so baixar `alturaDoOlho` (transicao de 0,15 s): o raycaster e a balistica ja usam
  esse valor, entao camera e tiro descem juntos de graca.

### Bot que parece gente
Ver o bot so andando de lado com os bracos abertos foi a reclamacao. O que resolveu:
- **Estado de combate por manobras:** parar para mirar (0,4-1,1 s) ou mover (0,5-1,3 s) para os
  lados, diagonal, avanco (longe) ou recuo (perto). Atirando andando: precisao x0,55 e cadencia
  x1,3. Bateu na parede, encerra a manobra.
- **Relogio da passada:** acumular a distancia andada (`passadaAnimada`) e usar
  `sin(passada * 9)` nas pernas, com joelho. Pernas paradas quando nao andou no passo.
- **Pose pela direcao vista da camera** (`deFrente = -(olharBot . dirCamera)`): de frente,
  cotovelos para fora e a arma no peito com a boca do cano escura; de perfil, a arma
  atravessada (baixa na patrulha); de costas, escondida. Coice recua a arma, clarao na boca.
- Conferir o desenho com uma **foto de cena fixa** em teste: [[foto-de-cena-em-teste-flutter]].

### Parede com faixas de altura (mureta, engradado, movel, janela)
Generalizacao da mureta: `Mapa.faixasDe(tipo)` devolve em que alturas a parede e **solida**
(mureta `[0; 0,48]`, janela `[0; 0,42]` e `[0,8; 1]`). O raio coleta toda parede com vao numa lista
`vazadas` e segue; bala, linha de visao e desenho perguntam `deixaPassar(tipo, altura)`.
`temLinhaDeVisao` passou a receber a **altura de quem olha**: em pe a janela deixa ver, agachado
o peitoril esconde - a cobertura ao contrario da mureta, sem caso especial.

### Porta sem geometria nova
Porta e celula de parede com estado (`abertura` 0..1): abre quando alguem esta a 1,5 celula e fecha
quando esvazia, entao nunca fecha em cima de ninguem. Fisica binaria (passa com abertura >= 0,55),
desenho continuo: enquanto abre, a folha encolhe para a dobradica (`u / (1 - abertura)`) e o resto
da face vira sombra; aberta, ela entra na lista de vazadas so para desenhar a folha dobrada. A rota
do bot trata porta fechada como caminho. **Guarde `celulaX/Y` no `Raio`**: arredondar o ponto da
batida cai na celula vizinha, porque a batida fica na borda.

### Chao desenhado com o mesmo DDA
Raycaster classico nao desenha chao, e o cenario parece flutuar. Por coluna, caminhe a grade como o
raio da parede ate o z-buffer e, em cada celula, risque a marca do piso: junta de azulejo e tabua na
**borda** (distancia de entrada), onda/tufo/ponto no **meio**. `y = horizonte + (altura/d) * olho`.
Divisa entre dois pisos vira traco firme - e o contorno da piscina. Pule textura quando a celula tem
menos de ~3,5 px na tela, senao o chao longe vira faixa cheia.

### Desenho na face da parede
Detalhe em coordenada da face (`ondeNaParede`) e altura da celula gruda na parede: risco horizontal
por coluna forma linha continua; traco vertical so na coluna em que `|u - alvo| < passoU`
(`passoU = larguraDaColuna / larguraDaCelulaNaTela`). Acabamento por mapa (tijolo, reboco, chapa,
bloco) diferencia cenarios tanto quanto papel e tinta.

### Teto e altura do pulo: o jogador nunca pode ver o mundo de fora
Raycaster com teto em 1 e olho em 0,5 e uma caixa sem tampa: tudo acima do topo das paredes e
folha. Duas regras juntas fecham a caixa: (1) **o olho nunca passa de 1 - folga da cabeca** - o
pulo e limitado e a cabeca bate no teto; (2) **desenhe o teto**: fundo sombreado de 0 ao
horizonte antes das paredes, e as marcas depois delas, com o DDA do chao espelhado
(`y = horizonte - (altura/d) * (1 - olho)`). Muitas marcas por quadro: junte em `Float32List` e use
`canvas.drawRawPoints(PointMode.lines, ...)`, em lotes por distancia para variar a tinta.

### Subir em cima de algo: colisao com altura
O corpo vira um intervalo vertical `[pes, pes + altura]`. Uma celula bloqueia se alguma faixa solida
dela cruza esse intervalo, ou se o intervalo passa de 1 (teto). O chao sob o corpo e o topo mais
alto de faixa abaixo dos pes em qualquer um dos quatro cantos - basta um canto em cima para ficar
de pe, e so cai quando o corpo inteiro sai. A gravidade leva os pes ate esse chao. Escolha as
alturas pelo teto: com olho em 0,5 = 1,6 m, 1 = 3,2 m; o que se sobe tem de caber com o corpo
inteiro embaixo do teto (engradado 0,38 + corpo 0,57 < 1; mureta 0,48 + 0,57 > 1).
Visto de cima, a parede baixa precisa do **tampo** (da borda de entrada a de saida da celula),
senao parece oca e mostra o chao de tras.

### Zoom de luneta num raycaster
A altura da parede na tela e `altura_da_tela / distancia` - nao depende do campo de visao. Estreitar so
o plano da camera (`abertura / aumento`) aproxima na horizontal e **achata** tudo. O zoom de verdade
multiplica a projecao vertical pelo mesmo aumento (`projecao = altura_da_tela * aumento`) em todo lugar
que projetava altura, **inclusive** no deslocamento do horizonte pela mira e pelo recuo; senao a bala
deixa de ir para onde a mira aponta. Sensibilidade do mouse dividida pelo aumento. Conferir numa foto:
um ponto fixo da cena tem de se afastar do centro exatamente `aumento` vezes.

### Billboard translucido na fila dos opacos
Nuvem de fumaca desenhada depois de todos os bots cobre ate quem esta na frente dela. Ponha a nuvem na
mesma fila de desenho por distancia (longe primeiro): antes de cada bot, desenhe as nuvens mais
distantes que ele; no fim, as que sobraram. Contorno de todas as bolhas antes dos preenchimentos faz a
nuvem ler como uma coisa so.

### Projetil em grade com faixas de altura
Granada e ponto com velocidade 3D, eixo a eixo como o corpo: celula e solida se `!deixaPassar(tipo, z)`;
chao e o topo da faixa abaixo de `z`; rebate invertendo o eixo com perda, e perde velocidade horizontal a
cada batida no chao. Com isso ela passa por cima da mureta, bate na janela e para no tampo do balcao sem
fisica nenhuma alem da grade.

## Armadilhas
- Pulo que leva o olho acima do topo das paredes mostra o cenario "de fora" - parece que o mapa
  some. Limite o pulo pelo teto, nao so pela gravidade.
- Fiada de tijolo **mais** hachura vertical vira papel quadriculado: detalhe de face substitui a
  hachura (reboco, que e liso, mantem a hachura).
- Amostrar o chao em distancias fixas erra a junta do azulejo; use DDA pela borda da celula.
- Espalhar tufo/ponto com `(x*5 + y*9).floor().isEven` sai em listra diagonal; use hash com xor.
- **`lista.length = n` numa `List<double>`** preenche com `null` e estoura em execucao; o
  analisador nao pega. Usar `List<double>.filled`.
- **Conferir o alcance so no topo do laco do DDA** deixa passar parede alem do alcance - e a
  bala acerta fora do alcance da arma. Conferir depois do passo e antes de aceitar o acerto.
- **Numero magico de altura de tela** (`tamanho.height / 720`) faz a arma sumir fora da borda em
  telas de outra altura. Escala sempre relativa.
- **Gatilho em reconhecedor de gesto e furado.** `onTapDown` junto de `onPanUpdate` dispara ao
  **comecar** o arrasto (girar a mira gastava bala); trocar por `onTap` perde disparo em
  sequencia rapida. O certo num jogo de tiro e `Listener` com ponteiro cru: **dispara na hora
  ao apertar** e segue disparando enquanto preso, no ritmo da cadencia.

## Relacionado
- [[RagdollGames]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[forge2d-e-box2d-v3]]
