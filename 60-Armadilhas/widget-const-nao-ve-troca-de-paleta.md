---
tipo: armadilha
titulo: Widget const nao ve troca de paleta - texto some no modo dark
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/armadilha, stack/flutter]
palavras-chave: [const, widget, paleta, tema, modo escuro, dark mode, rebuild, KeyedSubtree, cor, build]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: alta
---

# Widget const nao ve troca de paleta
## Resumo
Com cores lidas de uma fachada estatica (`Cores.tintaPreta` como getter), um widget criado
como `const` **nao e reconstruido** quando a paleta muda - e fica com a cor velha.

## Contexto
No menu do `counter-ragdoll` (2026-09-10). Ao ligar o modo dark na tela de Opcoes, o titulo
"Opcoes" sumiu e os nomes das secoes ficaram quase invisiveis: tinta escura sobre papel
escuro. As telas abertas **depois** da troca vinham certas, o que despistou.

## Detalhe

### Por que acontece
`const _Titulo('Opcoes')` e sempre a **mesma instancia**. Quando o pai reconstroi, o Flutter
compara o widget novo com o antigo, ve que sao identicos e **nao chama `build`** de novo. O
`build` e onde a cor e lida - entao a cor da paleta antiga fica.

O sublinhado vermelho continuou visivel porque o vermelho das duas paletas contrasta com
ambos os fundos. So o texto escuro sumiu.

### A correcao
Uma chave ligada ao estado que troca a paleta, envolvendo a arvore que le cores:
```dart
KeyedSubtree(
  key: ValueKey(configuracoes.modoEscuro),
  child: ...,
)
```
Chave nova, subarvore nova: todo widget reconstroi, `const` ou nao. Pega os widgets `const`
de hoje e os que alguem escrever amanha. O custo e perder estado de rolagem e hover naquele
instante - aceitavel numa troca de tema.

Alternativa mais "Flutter": `ThemeExtension` + `Theme.of(context)`, que registra dependencia e
reconstroi quem le. Vale quando o app for grande; para um menu, a chave resolve.

### Onde **nao** acontece
No jogo (Flame), o desenho le as cores **a cada quadro** no `render` - nao ha cache de widget.
Por isso a partida trocou de paleta sem precisar de nada.

### Como pegar antes
Trocar o tema **na propria tela** que exibe o controle, e nao so abrir uma tela nova depois -
a tela nova sempre vem certa.

Melhor que olhar: **teste de widget** que abre a tela, troca o tema ali e confere
`tester.widget<Text>(find.text('Opcoes')).style?.color`. No `counter-ragdoll` esse teste
(`test/presentation/menu_test.dart`) foi **sabotado de proposito** - chave do `KeyedSubtree`
trocada por uma fixa - e falhou com a cor da paleta clara, como devia. Teste que passa com e
sem a correcao e falsa confianca; sabotar e o jeito barato de saber.

## Relacionado
- [[RagdollGames]]
