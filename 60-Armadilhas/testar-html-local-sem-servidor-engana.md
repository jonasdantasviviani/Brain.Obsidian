---
tipo: armadilha
titulo: Testar HTML local sem servidor engana: o CSS nao carrega
projeto: [Sites]
stack: [html, css]
tags: [tipo/armadilha, stack/html]
palavras-chave: [file://, data url, css, preview, teste, local, servidor, http.server, falso negativo, stylesheet]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Testar HTML local sem servidor engana: o CSS nao carrega
## Resumo
Abrir um HTML por `file://` ou preview pode carregar a pagina como `data:` URL, onde `href` relativo nao resolve e nenhum CSS externo entra.

## Contexto
Aconteceu em 07/09/2026 ao validar o honeypot dos [[Sites-Estaticos]].

## Detalhe

### O sintoma
O campo que deveria estar escondido a `-9999px` aparecia na tela.
`getComputedStyle` retornava `left: auto`, `position: static` — como se o CSS nao existisse.

### A causa
A pagina tinha sido carregada como `data:text/html;...`. Nesse contexto **nao ha URL base**,
entao `<link rel="stylesheet" href="style.css">` nao resolve para lugar nenhum.
O diagnostico foi direto:

```js
document.styleSheets.length        // 0
getComputedStyle(document.body).fontFamily   // "Times"  ← fonte padrao do navegador
getComputedStyle(document.body).margin       // "8px"    ← margem padrao
location.href.startsWith('data:')            // true
```

`Times` + `8px` de margem = **nenhum CSS aplicado**. Sempre confira isso antes de concluir
qualquer coisa sobre layout.

### A correcao
Servir por HTTP de verdade:
```bash
cd site/ && python3 -m http.server 8899 --bind 127.0.0.1
```
Com o CSS carregado, o mesmo teste deu `left: -9999px`, `position: absolute` — correto.

### A licao maior
Um teste que **falha** por causa do ambiente e tao ruim quanto um que **passa** por engano.
Antes de agir sobre um resultado inesperado, valide se o ambiente do teste representa o real.
Aqui, um `styleSheets.length` teria economizado a conclusao errada.

## Relacionado
- [[honeypot-anti-bot-em-formulario]]
- [[Sites-Estaticos]]
