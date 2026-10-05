---
tipo: armadilha
titulo: Flutter reusa o estado do TextField ao trocar de pagina no mesmo lugar
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/armadilha, stack/flutter, ui, estado]
palavras-chave: [TextEditingController, StatefulWidget, key, ValueKey, initState, reuso de estado, element, didUpdateWidget, campo de texto, menu]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Estado de campo reusado entre paginas

## Resumo
Trocar o conteudo de uma tela (um `switch` de paginas dentro do mesmo `Column`) mantem o `State` de
um `StatefulWidget` que ocupa **a mesma posicao e o mesmo tipo**. Um campo que cria o
`TextEditingController` no `initState` a partir de `widget.valor` herda o texto da pagina anterior.

## Sintoma
No menu do Counter-Ragdoll, o campo "Nome da sala" abria escrito "Voce" - o texto do campo "Seu nome"
da pagina de antes. O `onChanged` ja apontava para o widget novo, entao digitar mudava a coisa certa;
so o texto inicial estava errado.

## Causa
O Flutter compara tipo + posicao (e chave) para reaproveitar o `Element`. Os dois campos eram
`_Campo` no mesmo indice do `Column`, entao o `_CampoState` foi reusado e o `initState` nao rodou.

## Correcao
Chave por campo: `super(key: ValueKey('campo:$rotulo'))`. Alternativa: tratar `didUpdateWidget`
e reescrever o controller quando o valor de origem muda.

## Como pegar
O widget test que so tocava nos botoes passou; o bug apareceu **olhando** a tela no navegador.
Teste de regressao: `find.widgetWithText(TextField, 'Sala de Jonas')` depois de trocar de pagina.

## Relacionado
- [[verificar-jogo-no-navegador-do-painel]]
- [[2026-09-12-counter-ragdoll-capacete-placar-cenarios-lobby]]
