---
tipo: armadilha
titulo: Comentario em portugues que comeca com "todo" e lido como TODO pelo lint
projeto: [RagdollGames]
stack: [dart, flutter]
tags: [tipo/armadilha, stack/dart, area/ci]
palavras-chave: [flutter_style_todos, very_good_analysis, lint, comentario em portugues, todo, analise estatica]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# Comentario que comeca com "todo" vira TODO mal formatado

## Resumo
`flutter_style_todos` (ligado pelo `very_good_analysis`) reprova qualquer comentario cujo texto
**comece** com "todo", em qualquer caixa. Em codigo escrito em portugues isso acontece sozinho:
"todo mundo", "todo carro", "todo campeonato".

## Contexto
`need-for-ragdoll`, PR #16. Uma linha de doc:

```dart
  /// A `Corrida` devolve um ponto **no meio do eixo**, e ele e o mesmo para
  /// todo mundo que estava no mesmo trecho: dois rivais socorridos no mesmo
```

derrubou o job `analise` inteiro com:

> info • To-do comment doesn't follow the Flutter style • need_for_ragdoll_game.dart:858:3 •
> flutter_style_todos

## Detalhe
- A regra olha o **inicio do texto do comentario**, entao as outras dezenas de "todo mundo" do
  repo passam: elas estao no meio da linha.
- Quebra de linha automatica e o que cria o problema: a frase estava correta, so calhou de a
  quebra jogar "todo" para o comeco da linha seguinte.
- `flutter analyze` reprova em `info`, entao isto **para o CI** como se fosse erro.

## Como evitar
Ao quebrar linha de comentario, nao deixe "todo" (nem "TODO", "ToDo") abrir a linha. Reescreva:
"o mesmo para quem estava no mesmo trecho".

## Relacionado
- [[ci-verde-nao-ve-pixel]]
- [[RagdollGames]]
