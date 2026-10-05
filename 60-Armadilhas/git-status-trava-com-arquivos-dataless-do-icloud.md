---
tipo: armadilha
titulo: git status/commit/gh pr create travam com arquivos dataless do iCloud
projeto: [Eden]
stack: [git, macos, icloud]
tags: [tipo/armadilha, stack/git]
palavras-chave: [dataless, icloud, git status trava, index.lock, brctl, gh pr create]
origem: claude-code
criado: 2026-10-04
atualizado: 2026-10-04
confianca: alta
---

## Resumo
Com disco quase cheio, o iCloud evicta arquivos de repos em ~/Documents; git status/commit e gh pr create ficam parados em read().

## Contexto
Worktree do Eden em ~/Documents/Repos, 4,7 GB livres. `ls -lO` mostrava `hidden,compressed,dataless`; `sample <pid>` mostrou git em read(); varios git (app, GitHub Desktop) travados e um index.lock de 0 bytes sobrando.

## Detalhe
- Diagnostico: `find . -flags +dataless | wc -l`; `brctl download .` baixa, mas ~1 arquivo/s.
- Contorno rapido: commit por plumbing (hash-object -w, update-index --cacheinfo, write-tree, commit-tree, update-ref), `git push`, PR por `gh api .../pulls` (gh pr create faz git status).
- Matar os git pendurados e remover index.lock 0 bytes antigo, so depois de confirmar que nao ha git escrevendo.
- Docker: `docker builder prune` nao devolve espaco ao macOS na hora (Docker.raw so encolhe depois / ao reiniciar o Docker Desktop).
- Definitivo: liberar disco e/ou tirar repos/SDKs de ~/Documents (Flutter ja foi para ~/dev/flutter).

## Relacionado
[[icloud-evicta-node-modules-e-tsc-trava]] [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]] [[Eden]]
