---
tipo: decisao
titulo: Flutter puro em vez de Flame nos jogos idle
projeto: [Games]
stack: [flutter, dart]
tags: [tipo/decisao, stack/flutter, dominio/jogos]
palavras-chave: [flame, engine, game engine, flutter puro, custompainter, animatedbuilder, idle, merge, sprite]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: media
---

# Flutter puro em vez de Flame nos jogos idle
## Resumo
Nos jogos de idle, clicker e merge do [[Games]] nao entra engine: `AnimatedBuilder`,
`CustomPainter`, `Draggable` e `Ticker` cobrem tudo.

## Contexto
Decidido no planejamento do [[Games]] (2026-09-09). O reflexo de "jogo = engine" levaria a
`flame` por padrao nas seis ideias.

## Detalhe

### Decisao
| Ideia | Genero | Engine |
| --- | --- | --- |
| Frota, Deep Miner, Um Botao | idle / incremental | **Flutter puro** |
| Cozinha Infinita | merge em grade | **Flutter puro** (`Draggable`/`DragTarget`) |
| Torre de Cartas | roguelite de carta | Flutter puro provavel - carta e widget |
| Jardim de Automatos | factory em grade | **avaliar `flame`** - simulacao de grade grande por tick |

### Por que
Idle nao tem sprite, nao tem camera, nao tem colisao, nao tem cena. O que ele tem e **layout,
numero e animacao de barra** - exatamente o que o Flutter faz melhor que engine nenhuma.
Puxar `flame` para um idle e carregar componente, camera e game loop proprio sem usar nada disso,
e ainda brigar com o sistema de widget para fazer a UI, que e 100% da tela.

### Alternativa descartada
**`flame` em tudo.** Descartada por peso e atrito: a tela de um idle e uma lista de linhas com
botao e barra de progresso. Em `flame` isso vira overlay de widget sobre um jogo vazio - ou
seja, Flutter puro com passos a mais.

### Quando reverter
Quando houver **simulacao densa desenhada a cada tick** - a grade do Jardim de Automatos e o
caso. Ai `flame` (ou `CustomPainter` com dirty region) deixa de ser luxo e vira desempenho.

## Relacionado
- [[Games]]
- [[motor-idle-em-flutter]]
- [[flutter]]
- [[tecnologia-mais-chata-que-resolve]]
