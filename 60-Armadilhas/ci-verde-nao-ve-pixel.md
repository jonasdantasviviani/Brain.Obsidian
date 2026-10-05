---
tipo: armadilha
titulo: CI verde nao ve pixel - defeito visual atravessa PR aprovado
projeto: [RagdollGames]
stack: [flutter, flame, github-actions]
tags: [tipo/armadilha, stack/flutter, dominio/jogos, jogo/ragdoll, area/ci]
palavras-chave: [flame priority, viewport, camada, HUD escondido, componente opaco, teste de camada, artefato jogo-web, ver na tela, regressao visual]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# CI verde nao ve pixel

## Resumo
Os quatro jobs do `need-for-ragdoll` (analise, testes, fisica no Chrome, build web) provam que o
codigo **compila, passa e cabe**. Nenhum deles olha a tela. Todo defeito de camada, de cor e de
posicao passa por eles sorrindo.

## Contexto
PR #14 (23/09) entrou verde com a primeira cena em perspectiva. PR #15 (26/09) achou, olhando o
build do CI num navegador, tres defeitos que o verde nao pegou.

## Detalhe

### Os tres defeitos
| O que | Causa |
| --- | --- |
| A corrida em terceira pessoa **sem placar, sem contagem e sem barra de nitro** | A cena nasceu com `priority: 100` no viewport, acima do HUD (9), da contagem (10) e da zona do nitro (8), e pinta a folha inteira **opaca** |
| Na pista noturna, HUD, marca de pneu, zona do nitro e caneta da introducao **invisiveis** | Cor escrita na mao (`Cores.tintaPreta`) em vez de vir da paleta do tema |
| O placar de drift escrito **por cima do botao de pausa** | Dois donos diferentes do mesmo canto: o HUD e do Flame, o botao e um widget do Flutter |

### O que trancou cada um
- **Camada**: o teste compara a camada da cena com a **dos componentes de verdade**
  (`expect(cena.priority, lessThan(HudComponent(...).priority))`), em vez de repetir o numero.
  Assim ele continua valendo quando alguem mudar a camada do HUD.
- **Cor**: o teste mede **contraste de claridade** (`Color.computeLuminance`) entre tinta e papel
  para os quatro temas de uma vez. Tema novo que nasca ilegivel reprova sozinho.
- **Posicao**: nenhum teste. Foi visto na tela, e a constante passou a carregar o motivo no
  comentario ("o canto ja tem o botao de pausa, 16 px de folga mais 28 px de botao").

### O jeito de ver a tela sem compilar aqui
```bash
gh run download <run-id> -n jogo-web -D /tmp/jogo
cd /tmp/jogo && python3 -m http.server 8391 --bind 127.0.0.1
```
O jogo e Flutter web: o build do CI abre em qualquer navegador. Teclado sintetico funciona para
dirigir sem mao (util para chegar a uma tela especifica):

```js
window.dispatchEvent(new KeyboardEvent('keydown',
  {key:'ArrowUp', code:'ArrowUp', keyCode:38, bubbles:true}));
```
Sem o `keyup`, o Flutter mantem a tecla **pressionada** - e assim o carro acelera sozinho.

## A regra
Tela nova, camada nova ou cor nova = **abrir o build do CI e olhar** antes de fechar o PR. O verde
diz que nada quebrou; ele nao diz que da para ver.

## Relacionado
- [[cara-de-need-for-speed-underground]]
- [[workflow-reutilizavel-exige-a-permissao-que-declara]]
- [[RagdollGames]]
