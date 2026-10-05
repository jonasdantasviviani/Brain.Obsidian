---
tipo: armadilha
titulo: Checkout local 25 commits atrás da main e ref duplicada do iCloud travando o git fetch
projeto: [BTech]
stack: [git, macos, icloud]
tags: [tipo/armadilha, git, icloud]
palavras-chave: [checkout desatualizado, refs/heads "2", bad object, fetch falha, did not send all necessary objects, worktree da origin/main, BTech.Web atras da main]
origem: claude-code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: alta
---
# Checkout local atrás da main + ref duplicada do iCloud

## Resumo
Em 01/10 `BTech.Web` e `BTech.NFe.Api` estavam em branches de segurança 25 e 31 commits atrás da `origin/main`. Trabalhar ali daria código sem o cadastro unificado de produto/serviço e telas que o Jonas já testa.

## Detalhe
- Antes de mexer: `git fetch` e `git log origin/main`; criar worktree em `_wt/<nome>` a partir de `origin/main` (`git worktree add -b <branch> ../_wt/<nome> origin/main`). Os menus do app do Jonas ("Notas de produto (NF-e)"/"Notas de serviço (NFS-e)") só existiam na main.
- Na API o fetch falhava: `fatal: bad object refs/heads/feat/... 2` + `did not send all necessary objects`. Era o arquivo `.git/refs/heads/feat/<branch> 2`, duplicata criada pelo iCloud (nome com espaço). Mover o arquivo para fora (não apagar) resolve; o branch de verdade está em `packed-refs`.
- Worktree novo não tem `node_modules`: symlink do outro checkout enganou (faltava vitest da main) — `npm ci` no worktree.
- Servidor `next dev` neste disco leva ~18 min para ficar pronto e `dotnet`/`git status`/`grep -r` estouram 120 s: rodar com `run_in_background`.

## Relacionado
[[icloud-evicta-node-modules-e-tsc-trava]] · [[branch-mergeada-por-squash-e-apagada-no-remoto]]
