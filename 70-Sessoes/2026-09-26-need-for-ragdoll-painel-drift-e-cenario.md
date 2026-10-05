---
tipo: sessao
titulo: Need for Ragdoll - o painel, o drift, o cenario, a largada consertada e o modo arrancada
projeto: [RagdollGames]
stack: [flutter, dart, flame, github-actions]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll, area/ci]
palavras-chave: [PR 15, painel, velocimetro, conta-giros, placar de drift, paleta, cenario, poste, predio, noite, adesivos, largada, tecla B, startup_failure, ver na tela, artefato jogo-web]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# O painel, o drift na tela e a rua dos dois lados

## Resumo
Doze commits no PR #15, todos com o CI como unico juiz, e **pela primeira vez o jogo foi aberto
num navegador no meio do trabalho**, e nao so no fim. Foi isso que achou tres defeitos que dois
PRs verdes tinham deixado passar - ver [[ci-verde-nao-ve-pixel]].

## Contexto
Continuacao de [[2026-09-23-need-for-ragdoll-escopo-underground-e-ci-verde]]. O Marco 1 (a corrida
vista de tras) tinha entrado no PR #14; faltava o que o dono pediu como "pacote de alusao" em
[[cara-de-need-for-speed-underground]].

## Detalhe

### O que estava quebrado e ninguem sabia
1. **A perspectiva escondia o HUD inteiro.** `priority: 100` no viewport, acima do placar (9), da
   contagem (10) e da barra de nitro (8), com fundo opaco.
2. **A folha noturna apagava o HUD**, a marca de pneu, a zona do nitro e a caneta da introducao:
   cor escrita na mao. E a pista noturna liberava tao tarde que o dono nunca a tinha visto.
3. **O workflow `dependencias` morria em `startup_failure` em todo PR desde 23/09** -
   [[workflow-reutilizavel-exige-a-permissao-que-declara]].
4. **A main esta vermelha na analise desde 23/09**: o ultimo commit do PR #14 entrou depois da
   rodada verde. Merge rapido demais nao testa o ultimo commit.
5. **Os quatro adesivos da garagem eram inalcancaveis**: nenhuma linha do roteiro liberava adesivo.

### O que o jogo ganhou
| Peca | Nota |
| --- | --- |
| **O painel** | Mostrador redondo, velocidade em km/h no meio, marcha embaixo. O ponteiro e o **giro**, nao a velocidade: cai na troca de marcha e sobe enquanto a marcha estica, e e essa serra que o olho le como aceleracao. As relacoes sao as do `Motor` do drag - um carro, um cambio |
| **O placar de drift** | Liga o `ContadorDeDrift` que estava solto desde o PR #14. Dois numeros: o garantido e o que ainda esta na mao do jogador |
| **O cenario** | Predio, alambrado, poste e letreiro de 16 em 16 m dos dois lados, deterministico pelo id da pista. Poste de tres em tres nos dois lados (e o par passando que da o ritmo), predio so em reta (em curva ele tapa a pista) |
| **A paleta** | Papel e quatro tintas por tema, passados por parametro. Teste mede **contraste de claridade**, nao igualdade de cor |
| **A capa** | "Debaixo da Carteira" - a palavra *underground* pelo preco de uma frase |
| **A noite** | Liberada na primeira corrida, e nao no fim do campeonato 3 |
| **A tecla B** | `arremessarMotorista()` existia desde o primeiro sprint **sem chamador** |

### O que o CI cobrou em cinco rodadas
- `undefined_getter`: dentro de `LargadaEmCena` o campo `jogo` e `JogoComFisica` (infraestrutura),
  que nao conhece paleta. Quem sabe a pista e quem monta a largada.
- `missing_method_parameters` num `static double fracaoDoVermelho => ...`: **getter estatico sem o
  `get`** vira metodo sem lista de parametros. O analisador diz isso de um jeito que nao ajuda.
- Invariante que eu nao conhecia: `campeonato.pistas.toSet()` tem de ser **exatamente** o conjunto
  de pistas liberadas ate ali. Liberar a noite cedo obrigou a poe-la nos campeonatos 1 a 3.
- Grade: `fracaoLateralDaGrade` (fracao da largura) apertava os dois carros numa pista de 10 m.
  Virou medida em metros com teto pela largura. E 1,6 m do centro a parede encostava na curva
  (1,5958 medido); agora 1,9.

### O que foi visto na tela (build do CI, navegador local)
Funciona: menu com a capa nova, corrida em perspectiva **com HUD**, mostrador com ponteiro e
marcha, cenario dos dois lados lido como rua, troca de camera (C), vista de cima com o boneco e as
marcas de pneu, placar de drift aparecendo ao atravessar.

**Achado que fica em aberto:** com o jogador parado na largada, os rivais ainda o empurram e o
motorista sai pela janela. A grade mais larga (8 m entre fileiras) diminuiu, nao resolveu. A pista
do problema esta em `PilotoDeRival._aliviarPerto`: o modelo de frenagem dele e otimista
(`sqrt(2*freio*vao) + velocidadeDeConvivencia`, com `velocidadeDeConvivencia = 3`), entao ele mira
uma velocidade que nao consegue dissipar. **Nao mexi**: tuning de IA sem olhar o jogo rodando e
chute, e o dono precisa dizer se encostao faz parte.

## A segunda metade do dia: PR #16 e #17

### O "TP dos bots" (PR #16)
O dono abriu o jogo e viu os bots sumirem do lugar logo depois da bandeira. A cadeia inteira esta
em [[largada-parada-vira-encalhe-e-o-socorro-teleporta]]: o detector de encalhe acusa a largada
inteira, e o socorro leva todos para o **mesmo** ponto do eixo. Tres cortes: carencia a partir do
verde, fila deixa de contar como encalhe, e uma faixa de retorno por lugar da grade.

No mesmo PR, o que o dono pediu junto: **botao de camera na tela**, com o nome do angulo escrito
ao lado (no celular nao existe tecla C - os cinco angulos do Underground nao existiam para quem
joga com o dedo), e **Controles** dentro da pausa, porque e no meio da corrida que a duvida
aparece.

### O modo arrancada (PR #17)
Primeiro dos modos que **tinham dominio escrito e nenhuma tela**. `ProvaDeArrancada` nao usa fisica
nenhuma: quem anda com o carro e o `Motor` do drag, que ja integra a propria velocidade; a prova so
acumula distancia, e por isso roda inteira num teste em milissegundos.

A tela tambem nao usa Flame nem Box2D - e um `CustomPainter` com um relogio de passo fixo, usando a
**mesma lente do circuito** (`Projetor`) com a camera presa atras do carro. Montar isto sobre o
jogo de circuito seria carregar uma fisica inteira para nao usar nada dela.

Tres metades, como no original: a largada e reflexo (sair antes do verde queima), o cambio e a
prova (verde rende, esticar esquenta, motor estourado acaba tudo) e **a rua tem transito** - quatro
faixas, carro de 95 em 95 m ciclando por tres delas (nunca a do rival, nunca duas seguidas na
mesma), e e o transito que faz a escolha de faixa importar enquanto se pilota o giro. A troca de
faixa e uma decisao: pedido novo so vale com a troca anterior terminada, senao uma tecla repetindo
a 60 Hz atravessaria a rua num piscar.

O rival e deterministico: a dificuldade dele e o **atraso** entre o ponteiro entrar na janela e a
mao puxar o cambio.

### Lints novos que o CI ensinou
- `createTicker` num campo `late final` explode: ele precisa do `TickerMode` da arvore, que so
  existe com o `State` montado. O relogio nasce no `initState`.
- Comentario que **comeca** com "todo" e lido como TODO -
  [[comentario-que-comeca-com-todo-vira-todo-mal-formatado]].
- `unnecessary_import` de `dart:ui` ao lado de `widgets.dart` (esta ligado, apesar de nao estar na
  lista do very_good_analysis).

## Pendente
- Tuning na garagem (as 9 pecas existem no dominio, a tela nao as mostra; falta decidir como se
  ganha peca, ja que o roteiro diz "sem loja, sem moeda").
- Modo arrancada: motor e comando prontos no dominio, sem tela.
- A largada amontoada, acima.
- Testar no Safari do iPhone e medir 60 fps.

## Relacionado
- [[ci-verde-nao-ve-pixel]]
- [[workflow-reutilizavel-exige-a-permissao-que-declara]]
- [[cara-de-need-for-speed-underground]]
- [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]
- [[RagdollGames]]
