---
tipo: sessao
titulo: Rabisco Games hub - versionado e com o catalogo inteiro por categoria
projeto: [Rabisco-Hub, RagdollGames]
stack: [html, css, python, git]
tags: [tipo/sessao, stack/html, dominio/jogos]
palavras-chave: [hub, git, RabiscoGames/hub, catalogo, 84 jogos, categoria, filtro, has, radio, jogos.json, gerar.py, modelo.html, quadrado, boneco, site/]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Rabisco Games hub - versionado e com o catalogo inteiro

## Resumo
Jonas: "Crie um projeto no git e versione este hub. Ja separe o hub entre categorias. Eu irei criar
todos os jogos listados neste arquivo [catalogo-de-franquias.md]. Entao se ja quiser deixar um
quadrado para cada com filtro de categoria".

## Detalhe
- **Git**: `git init -b main`, auditoria de segredos, commit `bc0b92d` com o hub como estava,
  `gh repo create RabiscoGames/hub --private --source . --remote origin --push`.
- **Reestrutura**: o que vai ao ar foi para `site/`; `dados/jogos.json` (11 categorias, 84 jogos),
  `modelo.html` e `gerar.py` geram `site/index.html` e `site/filtros.css`. `servir.py` passou a
  servir `site/` e a aceitar como jogo os slugs do JSON. Ver [[hub-gerado-por-script-sem-js]].
- Textos do catalogo reescritos com acento e sem jargao interno ("o mais direto de todos" virou
  "Corrida de obstaculos em que todo mundo ja nasce mole"). A Ref. interna e o risco nao foram para
  o JSON.
- Pagina: "Jogue agora" (3 cartoes grandes) + "Todos os jogos" com filtro fixo e quadrados;
  boneco rabiscado no lugar da capa que nao existe.

### Verificacao
- `gerar.py`: 84 quadrados, 3 cartoes, 11 secoes, 12 radios, 11 regras; `--conferir` em dia;
  aninhamento de tags conferido com `html.parser`; JSON quebrado de proposito recusado com 6 erros.
- `curl`: hub com CSP, `filtros.css` 200, `base href` reescrito nos 3 builds, `/call-of-ragdoll/`
  404 "ainda nao tem build", `servir.py`/`gerar.py`/`dados/jogos.json`/`README.md`/`modelo.html` 404.
- Navegador: clique real no chip "Corrida" deixa so a secao `corrida` com 9 quadrados; sem rolagem
  horizontal (`scrollWidth == clientWidth`).

### Tropecos
- `innerWidth` inclui a barra de rolagem: `scrollWidth > innerWidth` acusa rolagem horizontal falsa.
  Comparar com `document.documentElement.clientWidth`.
- `document.images` com `complete` falso eram so as capas `loading="lazy"` abaixo da dobra.
- O painel pinta tela em branco depois de `window.scrollTo` + screenshot imediato; esperar 2 s.
- Clique por coordenada no painel exige screenshot antes no mesmo documento; clique por `ref` do
  `find` nao.

## Pendencias
- Nomes repetidos do catalogo (Super Ragdoll Bros x2; Ragdoll Royale e The Elder Ragdolls tambem
  sao reservas) - decisao do Jonas.
- Capas com conflito de marca (lista em [[RagdollGames]]), hospedagem, fonte propria.

## Relacionado
- [[Rabisco-Hub]]
- [[hub-gerado-por-script-sem-js]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[2026-09-12-rabisco-hub-de-jogos]]
- [[verificar-jogo-no-navegador-do-painel]]
