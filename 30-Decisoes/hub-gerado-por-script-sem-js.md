---
tipo: decisao
titulo: Hub gerado por script a partir de um JSON, sem JavaScript no navegador
projeto: [Rabisco-Hub, RagdollGames]
stack: [python, html, css]
tags: [tipo/decisao, stack/html, dominio/jogos]
palavras-chave: [gerador estatico, gerar.py, jogos.json, string.Template, sem javascript, csp script-src none, filtro css, has, radio, catalogo, fonte unica, build]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# Hub gerado por script, sem JavaScript no navegador

## Resumo
Os 84 jogos vivem em `dados/jogos.json`; `python3 gerar.py` escreve o `site/index.html` e o
`site/filtros.css`, que vao versionados. O filtro por categoria e CSS (`:has()` + radio).

## Contexto
Jonas pediu um quadrado por jogo do catalogo (84) com filtro por categoria. O hub ja tinha
`script-src 'none'` e era HTML escrito a mao com 5 cartoes.

## Detalhe

### Decisao
- **Fonte unica** em JSON; a contagem de cada filtro, a secao de cada categoria e a regra CSS de
  cada filtro saem dela.
- Gerador em **Python puro** (stdlib: `json`, `html.escape`, `string.Template`, `struct` para ler o
  tamanho da capa). Valida slug, categoria, estado, capa e `alt`; avisa nome repetido.
- A saida gerada **vai para o git** - hospedagem nenhuma precisa rodar build.
- Filtro:
  ```css
  .catalogo:has(#cat-corrida:checked) .categoria:not([data-categoria="corrida"]) { display: none; }
  ```
  uma linha por categoria, em `filtros.css` gerado (inline seria barrado pelo `style-src 'self'`).

### Alternativas
| Opcao | Por que nao |
| --- | --- |
| HTML de 84 cartoes a mao | contagem do filtro e secao divergem na primeira edicao |
| JSON lido por JavaScript no navegador | abre `script-src`, quebra sem JS e nao indexa |
| Gerador de site (Eleventy, Hugo, Astro) | dependencia e toolchain para uma pagina so - [[tecnologia-mais-chata-que-resolve]] |

### Trade-off
- Esquecer o `python3 gerar.py` deixa o site velho. Guarda: `python3 gerar.py --conferir` sai 1.
- `:has()` exige Chrome 105, Safari 15.4, Firefox 121. Sem ele o filtro nao esconde nada - todos os
  jogos aparecem, nada quebra.
- O estado do filtro nao vai para a URL (nao da para mandar "so Corrida"). Se precisar, `:target`
  com ancoras resolve sem JS.

## Relacionado
- [[Rabisco-Hub]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[hub-aponta-para-os-jogos-sem-iframe]]
- [[tecnologia-mais-chata-que-resolve]]
