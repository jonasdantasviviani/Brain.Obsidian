---
tipo: sessao
titulo: Eden - UX, GitHub e relatório de uso de IA (PRs 30-34)
projeto: [Eden]
stack: [dotnet, nextjs, postgres, ollama, flutter]
tags: [tipo/sessao, stack/nextjs]
palavras-chave: [eden, github, user-agent, uso de ia, ollama, responsivo, qa, sugerir pr]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

## Resumo
Setup local do Éden no Mac virou uma sequência de correções e recursos: PRs #30 a #34.

## Detalhe
- #30 golden do mobile (Flutter 3.47.6 em ~/dev/flutter); #31/#32 `User-Agent` no cliente do GitHub (sem ele o GitHub dá 403, e eu culpei o token por engano), `githubId`→`gitHubId`, efeitos (chuva/logo) desligáveis, sem `backdrop-blur`.
- #33 lista por organização com filtros, remover monitoria/token, `usePending` (carregando + trava de clique), layout responsivo (gaveta no celular, `.grid` base minmax(0,1fr)).
- #34 início clicável (links do GitHub em nova aba), "Sugerir PR" (cria Plano, ~107 s no qwen3:8b), relatório e tela de Uso de IA (`GET /v1/models/report`).
- QA visual: banco `eden_qa` + `seed-demo.sql` + API 8081 + web 3001, login de teste em `.env.qa` (gitignored); auditoria no navegador de botões fora de card e `scrollWidth == innerWidth` em 375/768/1280.
- Armadilhas: comentário CSS com `*/` dentro quebrou uma regra; PR mergeado antes dos últimos commits (conferir `gh pr view` antes de empilhar); refs `HEAD 2` do iCloud travam o `git fetch`.

## Relacionado
[[Eden]] [[git-status-trava-com-arquivos-dataless-do-icloud]] [[mudar-o-vault-de-lugar-exige-atualizar-5-arquivos]]
