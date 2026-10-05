---
tipo: armadilha
titulo: Flutter web cancela o pointerdown - mousedown do documento nunca dispara
projeto: [RagdollGames]
stack: [flutter, dart, web]
tags: [tipo/armadilha, stack/flutter]
palavras-chave: [pointerdown, mousedown, mouseup, mousemove, preventDefault, pointer events, compatibilidade, flutter web, clique, listener]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: alta
---

# Flutter web cancela o pointerdown
## Resumo
Ouvinte de `mousedown` no documento **nunca dispara** em app Flutter web: o Flutter cancela o
`pointerdown`, e o navegador deixa de gerar os eventos de mouse de compatibilidade.

## Contexto
No `counter-ragdoll` (2026-09-10), ao mover o clique do jogo para ouvintes crus no documento.
Os cliques do **menu** funcionavam (Flutter), mas os da **partida** nao chegavam: o aviso de
captura nao sumia e a municao nao caia. Nenhum erro no console.

## Detalhe

### A regra da especificacao
Pointer Events: se o `pointerdown` for cancelado com `preventDefault()`, o navegador **suprime
os eventos de mouse de compatibilidade** daquele aperto - `mousedown`, `mouseup`, `click` e o
`mousemove` enquanto o botao estiver apertado. O Flutter web cancela o `pointerdown` (para nao
selecionar texto nem roubar foco), entao do lado do documento:

| Evento | Chega? |
| --- | --- |
| `pointerdown` / `pointerup` / `pointermove` | sim |
| `mousemove` **sem** botao | sim - por isso a mira pelo mouse funcionava |
| `mousedown` / `mouseup` | **nao** |
| `mousemove` **com** botao apertado | **nao** - girar segurando o tiro nao funcionaria |

### A correcao
Escutar eventos de **ponteiro**, filtrando `pointerType == 'mouse'`, em **fase de captura**:
```dart
final captura = true.toJS;
web.document
  ..addEventListener('pointermove', _movimento, captura)
  ..addEventListener('pointerdown', _aperto, captura)
  ..addEventListener('pointerup', _soltura, captura);
```
`PointerEvent` herda de `MouseEvent`: `movementX/Y` e `button` continuam la, e funcionam com o
ponteiro travado. Remover com a **mesma** opcao de captura, senao o ouvinte nao sai.

### Como foi confirmado
Trocar `mousedown` por `pointerdown` e nada mais: no painel, o primeiro clique passou a
capturar e o segundo a atirar.

### O sintoma que engana
O mouse **parece** funcionar: a mira pelo movimento (sem botao) responde, porque esse
`mousemove` nao e suprimido. So o clique e o arrasto somem. Se "o movimento funciona e o
clique nao", desconfiar disso primeiro.

## Relacionado
- [[raycasting-em-flutter]]
- [[RagdollGames]]
