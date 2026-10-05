---
tipo: armadilha
titulo: O navegador do painel nao serve para testar entrada de jogo
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [teste, verificacao, navegador, entrada, teclado, ponteiro, clique, automacao, harness, jogo]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-26
confianca: alta
---

# O navegador do painel nao serve para testar entrada de jogo
## Resumo
Da para **ver** o jogo no navegador embutido, mas nao da para **joga-lo**: a automacao entrega
um evento de ponteiro por carregamento e engole varias teclas.

## Contexto
Ao verificar o ragdoll de morte no `counter-ragdoll` (2026-09-10). Foi gasto tempo demais
tentando matar um bot pelo painel antes de perceber que o limite era do harness, nao do jogo.

## Detalhe

### O que falha
| Tentativa | O que acontece |
| --- | --- |
| Cliques repetidos, mesmo espacados | so o **primeiro** vira disparo |
| Tecla `f` | engolida (o navegador do painel usa) - `m` e `r` passam |
| `PointerEvent` sintetico via JavaScript | nao atravessa o Flutter |
| Arrasto longo para "segurar" o botao | o arrasto inteiro acontece **dentro de um quadro**, entao segurar nunca vale |
| Achar alvo que **anda** girando a camera | cada screenshot leva segundos e o alvo sai de quadro antes da foto - fotografar em teste ([[foto-de-cena-em-teste-flutter]]) |

### O que funciona
- **Arrasto simples (pan) funciona e repete**: no `ragdoll-go` (2026-09-12) girar a camera com
  `left_click_drag` deu certo seis vezes seguidas, e um arrasto para cima virou arremesso. O
  gesto inteiro cai dentro de um quadro, entao a duracao medida e quase zero e uma velocidade
  calculada por deslocamento / tempo sai **sempre no teto**.
- **Uma porta por jogo**: se a porta ja serve outro jogo, o servidor novo cai com
  `Address already in use` e a aba mostra o jogo errado **sem erro nenhum na pagina**.
- **Ver** a cena, medir desempenho, ler o console e a rede.
- Tecla avulsa que muda estado visivel (trocar mapa, trocar arma, recarregar) - **desde que o
  clique de foco venha no mesmo lote**: a aba perde foco entre chamadas.
- **Janela com tempo (loja, congelamento):** cada screenshot leva varios segundos de relogio
  do jogo. Fazer o fluxo inteiro - menu, clique, teclas - **num lote so**, com o screenshot so
  no fim; senao a janela fecha antes da tecla chegar e parece que o teclado nao funciona.
  Conferir se a tecla chegou com um `keydown` escutado por JavaScript antes de culpar o foco.
- **Gatilho preso:** o painel as vezes nao entrega o `pointerup` - o jogo acha que o botao
  segue apertado e dispara sozinho. Ler o estado (municao, reserva) antes de concluir algo.
- **O painel pode mudar de tamanho** entre lotes (ja virou 405x864): screenshot antes de
  clicar por coordenada.
- Desenho de coisa que se mexe rapido demais para o painel (bot atirando de perto):
  [[foto-de-cena-em-teste-flutter]].

### A regra
**Verificacao de mecanica vai em teste, nao em screenshot.** O que depende de entrada repetida
tem de ter a logica separada em codigo puro e testavel:
- acerto de bala -> `Partida.atirar()`, testado direto
- quem morreu desde o ultimo quadro -> `MortesNovas`, testado direto

Sobra para a tela so o que ela sabe responder: **como esta desenhado**. E honesto dizer ao
usuario o que ficou verificado por teste e o que ficou por olho.

### Nao caia na tentacao
Baixar a vida do bot, criar tecla de "matar todos" ou qualquer atalho **para conseguir tirar o
screenshot** e trocar a qualidade do jogo pela conveniencia da verificacao. Se nao da para
verificar por aqui, diz-se isso.

### A altura do painel muda entre telas (2026-09-12)
No menu do Counter-Ragdoll o quadro de coordenadas passou de 800x600 para 800x791 so por trocar de
pagina. Clique encadeado com coordenada da tela anterior cai em outro lugar - uma rodada de cliques
deixou a sala com mapa, dificuldade e tamanho trocados sem ninguem pedir. **Screenshot antes de
cada clique** e espera de ~2 s depois de navegar (com 1 s o segundo clique so marcou hover).

### Da para dirigir, sim: `KeyboardEvent` sintetico funciona (2026-09-26)
No `need-for-ragdoll`, dirigindo o build do CI pelo painel:

```js
const d = (tipo, key, code, keyCode) => window.dispatchEvent(
  new KeyboardEvent(tipo, {key, code, keyCode, which: keyCode, bubbles: true, cancelable: true}));
d('keydown', 'ArrowUp', 'ArrowUp', 38);   // sem keyup: a tecla fica PRESSIONADA
```

- **O `keydown` sintetico atravessa o Flutter web** (ao contrario do `PointerEvent`, que nao
  atravessa). Acelerou o carro, trocou a camera no `C` e reiniciou no `R`.
- **Omitir o `keyup` resolve o "nao da para segurar"**: o Flutter mantem a tecla em
  `keysPressed` ate chegar o `keyup`, entao o carro acelera sozinho enquanto voce tira
  screenshots. Era o limite que fazia parecer que o teclado nao funcionava.
- `computer{action:'key'}` do painel manda press+release: serve para tecla avulsa (trocar camera),
  nao para segurar acelerador.

### Aba escondida = jogo em camera lenta
`tabs_context` avisa "The Browser pane is currently hidden". Nesse estado o navegador estrangula
o `requestAnimationFrame`: medido no jogo, **7,66 s de cronometro em ~3 min de relogio** (~25x mais
lento). Consequencias:
- "a tecla nao chegou" costuma ser **o jogo nao andou**; confira pelo cronometro na tela antes de
  culpar o foco;
- `javascript_tool` tem teto de 45 s por chamada - `await` longo estoura; divida em chamadas;
- para qualquer coisa que dependa de tempo de jogo (largada completa, 3 voltas), conte com o
  estrangulamento ou desista e teste no dominio.

## Relacionado
- [[raycasting-em-flutter]]
- [[RagdollGames]]
