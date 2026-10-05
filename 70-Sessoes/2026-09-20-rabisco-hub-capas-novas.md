---
tipo: sessao
titulo: Rabisco Games hub - capas de Ragdoll FC, Angry Ragdolls e Among Ragdolls
projeto: [Rabisco-Hub, RagdollGames]
stack: [python, html]
tags: [tipo/sessao, stack/html, dominio/jogos]
palavras-chave: [capa, imagem nova, ragdoll fc, angry ragdolls, among ragdolls, sips, png disfarcado de jpg, jogos.json, alt, gerar.py]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Rabisco Games hub - tres capas novas

## Resumo
Jonas: "Coloquei algumas imagens novas de jogos na pasta, atualize". Tres capas entraram no hub.

## Contexto
Os arquivos estavam soltos em `site/img/jogos/`: `ragdoll-fc.jpg`, `angry-ragdoll.jpg`,
`among-ragdoll.jpg` - todos **PNG 1536x1024** com extensao `.jpg`, ~3,8 MB cada.

## Detalhe
- Convertidos para JPEG de verdade, 720x480, ~230-260 KB, **com o nome do slug**:
  `ragdoll-fc.jpg`, `angry-ragdolls.jpg`, `among-ragdolls.jpg` (os slugs no JSON sao no plural).
  ```bash
  sips -s format jpeg -s formatOptions 80 --resampleWidth 720 <original> --out site/img/jogos/<slug>.jpg
  ```
- Originais movidos para `~/.Trash/capas-originais-hub-2026-09-20/` (nada perdido).
- `dados/jogos.json`: `capa` + `alt` nos tres; `python3 gerar.py` e `--conferir` saem 0.
- Conferido no navegador (servir.py na 5120): as 11 `<img>` carregam com 720 de largura.
- Depois o Jonas pediu PR: commit `3837cf6` na branch `capas-ragdoll-fc-angry-among`, criada de
  `origin/main` (e nao da main local) para deixar de fora o `30516db` ainda nao enviado -> PR
  **RabiscoGames/hub#1** (o primeiro do repo). A main local continua um commit a frente do origin.
- Apareceu `hub/hub/` durante a sessao: clone de `RabiscoGames/hub` em 52e4a22 feito por outro
  processo (nao por esta sessao). Deixado intacto. O origin esta um commit atras do local (30516db).
- `preview_start` com launch.json falhou lendo `servir.py` - ver [[preview-start-nao-le-documents]].
  O `.claude/launch.json` criado para isso foi apagado; o repo nao ganhou arquivo novo alem das capas.
- Arquivos no fim: `dados/jogos.json`, `site/index.html` (modificados) e as tres capas (novas).
- O hook `Stop` rotulou a sessao como projeto "Cerebro" porque o cwd estava no vault quando ela
  terminou; o projeto real e o hub. Notas escritas por heredoc no Bash nao contam como edicao para
  o hook - usar Write/Edit no vault.

## Relacionado
- [[Rabisco-Hub]]
- [[hub-gerado-por-script-sem-js]]
- [[preview-start-nao-le-documents]]
