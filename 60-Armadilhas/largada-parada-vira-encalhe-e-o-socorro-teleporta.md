---
tipo: armadilha
titulo: Largada parada vira "encalhe" e o socorro teleporta o pelotao inteiro
projeto: [RagdollGames]
stack: [dart, flutter]
tags: [tipo/armadilha, dominio/jogos, jogo/ragdoll, area/ia]
palavras-chave: [ia de corrida, encalhe, socorro, reposicionar, largada, grade, teleporte, TP, ponto de retorno, transito]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# Largada parada vira encalhe, e o socorro teleporta todo o pelotao

## Resumo
Um detector de "carro encalhado" que diz **parado + querendo andar = preso** acusa a largada
inteira, porque largada e exatamente isso. Quando existe um socorro que reposiciona o encalhado,
o resultado na tela e o pelotao sumindo do lugar dois segundos depois da bandeira.

## Contexto
`need-for-ragdoll`, PR #16. O dono descreveu assim: *"os carros ficam parados na largada, os bots,
e quando iniciam eles dao TP saindo do lugar"*.

## Detalhe

### A cadeia, do primeiro elo ao ultimo
1. `PilotoDeRival` marca `_encalhado` com **2 s parado querendo andar acima de 4 m/s**;
2. na largada os seis carros estao parados querendo andar, e quem larga atras ainda espera o da
   frente sair;
3. `SocorroDoRival` ve `estaPreso` e manda recolocar;
4. `Corrida.pontoDeRetorno` devolve um ponto **no meio do eixo**, o mesmo para todos que estavam
   no mesmo trecho - entao os socorridos juntos voltam **um dentro do outro**;
5. dois corpos no mesmo lugar, a Box2D resolve arremessando os dois.

### Os tres cortes
| Elo | Corte |
| --- | --- |
| Largada e parada por definicao | O socorro ganha **carencia a partir do verde** (`largar()`), ligada ao evento de largada. 6 s cobrem a saida da grade inteira |
| Fila nao e encalhe | O piloto ja sabia quem esta na frente dele (usava para aliviar o pe). Esse mesmo vizinho passa a **impedir** o contador de encalhe. Quem pode estar preso de verdade e o da frente - e ele e socorrido, o que solta a fila |
| Dois carros no mesmo ponto | Faixa e recuo por lugar da grade: seis pares diferentes, ninguem volta em cima de ninguem |

## A regra que sai disto
**Todo detector de "travado" precisa saber quando o jogo ainda nem comecou.** Vale para largada,
para o carro recem-reposicionado (ja havia carencia para esse caso) e para qualquer pausa. E
**todo reposicionamento precisa de um lugar por carro**: um ponto so, compartilhado, e um
arremesso esperando acontecer.

## Relacionado
- [[ci-verde-nao-ve-pixel]]
- [[RagdollGames]]
