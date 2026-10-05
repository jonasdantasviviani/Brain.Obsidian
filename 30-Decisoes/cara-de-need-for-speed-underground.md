---
tipo: decisao
titulo: A cara do Need for Ragdoll - por que parece ferrorama, e a decisao do dono
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/decisao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [need for speed underground, ferrorama, camera, visto de cima, viewfinder angle, zoom, noite, neon, marca-texto, sensacao de velocidade, identidade visual, alusao, parodia]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-23
confianca: alta
---

# A cara do Need for Ragdoll

> **DECIDIDO PELO DONO em 2026-09-23**, depois de ler este documento:
>
> > "Ignore algumas decisoes do cerebro pensando no valor. Quero que seja o mais fiel ao NFS
> > Underground. Apenas em rabiscos mas tendo todas as mecanicas e angulos de camera. O mundo
> > aberto para chegar nas fases, a garagem, as atividades. Tudo."
>
> E, minutos depois, o ajuste que fecha o escopo:
>
> > "Siga com menus entao, mas a jogabilidade nas fases tem que ser a mesma. Pode comecar em
> > paralelo ate acabar tudo."
>
> Ou seja: **fidelidade acima de custo, sem mundo aberto**. Navegacao por menu, como no
> Underground 1; dentro das fases, o Underground inteiro - modos, angulos de camera, garagem,
> atividades. O rabisco e a unica licenca artistica. Isso **revoga**
> [[need-for-ragdoll-visto-de-cima-sem-gravidade]] (escolhida por custo) e responde as
> "decisoes do dono" listadas abaixo. Escopo e arquitetura em [[nfs-underground-fiel-em-rabisco]].

## As cinco respostas do dono (2026-09-23)
Perguntado depois do estudo de escopo e dos ceticos, ele decidiu:

| Pergunta | Resposta |
| --- | --- |
| O que incomodou mais | **Nao reconhecer o NFS** (alusao), nao a falta de velocidade |
| Mundo aberto | **Nao** - menu, como no Underground 1. Fidelidade total dentro das fases |
| A folha e o chao ou a tela | **A TELA**: pauta, margem e furos retos e fixos na tela, sem tremor, e o mundo em perspectiva desenhado por cima. Corrige o erro geometrico da "pauta como grade de fuga" (a pauta e world-locked em linhas de y constante: ela corre atravessada na pista) |
| O motorista voa | **Fisica de verdade**, num segundo mundo em plano vertical, **mais uma camera de batida** - porque com a camera atras do carro ele voa para LONGE de voce, e nao na sua direcao como eu tinha escrito errado |
| A Caneta (contrato da serie) | **Pode mudar, e vale para a serie**: desenha em pixel de pagina, nao em metros. Apaga a divida do `metrosPorPixel` final em cinco classes |
| Desnivel e viaduto | **Sim, quer viaduto** - o que reabre a pergunta de 3D, porque recorte em pe so e correto sobre plano. Estudo proprio em [[viaduto-e-desnivel]] |

## Resumo
Jonas abriu o jogo pela primeira vez com o texto funcionando e disse: "nao esta parecendo
o jogo do need for speed... esta mais parecendo um ferrorama". Ele tem razao, e a causa e
mecanica, nao falta de capricho. **Mas tres das quatro causas nao precisam mexer na camera.**

## Contexto
Levantado por 11 agentes (4 de referencia, 3 propostas, 1 juiz, 3 ceticos adversariais) em
2026-09-23. **Os tres ceticos refutaram o julgamento**, e o que esta aqui ja e a versao
corrigida. O que eles derrubaram esta na secao "O que quase foi vendido errado".

## Detalhe

### O diagnostico (conferido linha a linha no codigo)
| Causa | Evidencia |
| --- | --- |
| Camera travada ao norte: o carro gira, a folha nao | `viewfinder.angle` nao e escrito em lugar nenhum de `lib/` |
| Zoom fixo em 1,0 - so muda na camera lenta | `largada_em_cena.dart:303`, `sensacoes_em_cena.dart:229` |
| Carro ocupa ~8% da largura da tela | 96x48 px em ~40 m de mundo a 30 px/m |
| Folha bege iluminada por igual, meio-dia sempre | a pista `noite` so libera no campeonato 3 - o Jonas **nunca a viu** |
| Nenhuma pista visual de velocidade | rastro com alpha maximo 0,32, morre em 0,7 s |

O rabisco nao tem culpa: tremor, caneta e papel estao implementados como o contrato da serie
manda. O jogo traduziu toda a **mecanica** do Underground (drift, nitro, campeonato, cinco
rivais, garagem) e nenhuma das suas **marcas sensoriais**.

### As duas queixas sao diferentes - e o Jonas ja respondeu qual pesa
- **"nao parece Need for Speed"** = falta de **alusao**.
- **"parece um ferrorama"** = falta de **sensacao**.

**Perguntado em 2026-09-23, o Jonas respondeu: o que incomodou mais foi NAO RECONHECER O
NFS.** Ou seja, o problema e de **alusao**, nao de sensacao de velocidade. Isso reordena tudo:

1. O pacote de alusao (abaixo) passa para a frente. E o mais barato e o que ele pediu.
2. **A pergunta da camera cai de urgencia** - ela e remedio de sensacao. Continua em aberto,
   continua sendo decisao dele, mas nao e o proximo passo.
3. O zoom fechado deixa de ser o experimento numero um; vira item de sensacao, para depois.

### O pacote de alusao - o que faz bater o olho e dizer "isso e Need for Speed"
Nenhum destes depende da resposta da camera.

| Item | O que e | Onde |
| --- | --- | --- |
| **A noite** | Nao existe NFSU de dia. Unanimidade dos quatro dossies. Promover a noite a **segunda** corrida preserva a piada do "Underground" (ver decisao 3) | `roteiro_de_desbloqueios.dart:21`, `pistas.dart` |
| **O nome aludir** | Hoje o subtitulo e "o motorista nao usa cinto" e as pistas se chamam `recreio` e `patio de manobras` - dizem brinquedo, nao rua. **"Debaixo da Carteira"** poe a palavra *underground* na capa pelo preco de uma frase | `pista.dart:149,164`, menu |
| **O crew** | Certinho, Fundao, Regra, Copiador e Amassado ja existem com personalidade e nome na tela - mas aludem a **escola**, nao ao Underground. Apresentados como crew (cara, apelido, carro), viram a estrutura do original | `personalidade.dart`, `aviso_de_nome.dart` |
| **A garagem a mostra** | NFSU e o jogo do tuning cosmetico. Cinco carros e quatro adesivos ja existem e ninguem ve | `tela_da_garagem.dart` |
| **O painel** | A HUD de hoje ("1o /6", "volta 1/3") e placar de campeonato. Velocimetro redondo rabiscado com ponteiro tremendo e barra de nitro mudam o genero da tela | `hud_component.dart` |
| **O som** | Batida de caneta na carteira em laco, acelerando com a velocidade. Trilha e marca sensorial nº 1 do NFSU e cabe nos tokens que ja existem | `evento_de_som.dart` |
| **A tipografia** | O titulo em letra agressiva, no lugar do Roboto azul. O estudio ja tem a ferramenta que vira PNG em riscos de caneta (`tracos_do_logo.py`, no repo do hub) | menu, abertura |

### Sensacao - fica para depois, na ordem de melhor retorno
Zoom fixo mais fechado (~1 h), cenario dos dois lados da pista, linhas cineticas, rastro mais
forte. E, so entao, a conversa de camera.

### Divida tecnica que vale em qualquer caminho (nao decide nada)
1. **Fechar o zoom fixo** (item de sensacao, mas listado aqui pelo preco) - `1,0` para `~1,8`, duas constantes, ~1 h. Dobra o carro na tela
   (8% -> 16%). Responde de graca "o problema era so eu estar longe demais?". O repo ja sabe
   acertar a espessura do traco quando o zoom muda (`largada_em_cena.dart:276`).
2. **Cortar a pauta pelo retangulo visivel** - hoje `_desenharPauta` nao tem culling nenhum:
   ~349 `drawLine` de ponta a ponta da folha, **todo quadro**. Modelo copiavel ao lado:
   `PistaComponent._areaVisivel()`. Fazer **antes** de qualquer experimento de camera.
3. **A paleta chegar aos ~14 componentes com tinta fixa** - divida ja documentada pelo proprio
   repo (armadilha 9, `pista.dart:18-20`). Hoje a HUD e preta: invisivel sobre a folha noturna.
4. **Desenhar o que existe dos dois lados da pista** - predio em hachura, alambrado, poste,
   letreiro. Ferrorama e trilho no vazio; Underground e corredor. Desenho puro sobre o `Eixo`
   que ja existe: zero fisica, zero camera, sobrevive a qualquer escolha.
5. **Largada amontoada** - tres constantes em `construtor_de_pista.dart` (`entreFileiras = 5`
   com carro de 3,2 m deixa 1,8 m entre para-choques). 10 minutos.

### Decisoes que sao do dono
1. **A camera gira com o carro?** Mantem a fisica de cima e gravidade zero - a decisao
   registrada continua de pe, porque ela nunca colocou esta pergunta (ela decidiu "de cima
   contra 2.5D lateral"). **Cuidado:** girar com o carro no centro exato vira *prato
   giratorio* - so paga junto com deslocamento para a frente (carro baixo no quadro, a rua
   chegando de cima).
2. **Perspectiva em terceira pessoa (OutRun/Mode 7)?** 15 a 20 dias, arte nova dos cinco
   carros vistos de tras, e o risco de identidade mais fundo. O argumento a favor mais forte
   nao e realismo: **em perspectiva o motorista voa na sua direcao**, e essa e a piada central
   da serie. De cima, voce nunca ve o voo.
3. **A noite vira padrao?** Contraria `docs/roteiro.md` (campeonato 3) e quebra testes de
   `campeonato_test.dart` e `progresso_test.dart`. E ha um argumento de parodia contra:
   "Underground" so quer dizer algo se existir um "em cima" - comecar no recreio, de dia, e
   **descer** para a noite e a premissa do original. Promover a noite a **segunda** corrida
   custa o mesmo e preserva a piada.
4. **A pauta aparece na noite?** O codigo diz, em comentario, "sem pauta - como quando se
   olha a folha contra a luz" (`papel_component.dart:136-138`): e decisao de arte registrada,
   nao esquecimento. Trazer a pauta de volta em tinta clara com alpha baixo devolve a
   referencia de velocidade, mas muda a metafora.
5. **A tecnica do brilho** - nada de blur nem gradiente (regra 3 da identidade). Farol = duas
   retas mais hachura que rareia, um cone **desenhado**; underglow = risco de marca-texto por
   baixo do carro, que e objeto de mesa de aula, nao luz.

### O que quase foi vendido errado (os ceticos pegaram)
- **"Com a camera travada voce ve o traçado inteiro"** - **falso**. A camera segue o carro
  (`camera.follow`, `largada_em_cena.dart:304`) e mostra ~1/6 da pista. Era o argumento
  central que empurrava para a camera giratoria.
- **"432 testes de camera continuam verdes"** - 432 e o numero de **linhas** do arquivo; sao
  33 testes.
- **"A noite esta quebrada"** - nao esta: e escolha de arte escrita no codigo.
- **"Nada e jogado fora"** - a paleta sobrevive aos tres caminhos; o desenho da pauta e
  reescrito se um dia entrar perspectiva.
- **Esforco em dias foi lido no codigo, nunca medido** - ninguem conseguiu compilar.

### Pre-requisito de tudo
Destravar o ambiente de build: disco a 98% e SDK no iCloud. "Abrir e olhar" e hoje o
gargalo real - ver [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]].

## Relacionado
- [[need-for-ragdoll-visto-de-cima-sem-gravidade]]
- [[parodia-precisa-aludir]]
- [[fable-planeja-claude-code-implementa]]
- [[fisica-de-carro-arcade-visto-de-cima]]
- [[RagdollGames]]
