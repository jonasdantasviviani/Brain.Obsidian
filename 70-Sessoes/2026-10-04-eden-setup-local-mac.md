---
tipo: sessao
titulo: Setup local do Eden no Mac
projeto: Eden
stack: [dotnet, nextjs, postgres, docker, flutter]
tags: [setup, macos]
palavras-chave: [eden, setup local, docker, flutter, dart sdk, env]
origem: claude-code
criado: 2026-10-04
atualizado: 2026-10-04
confianca: alta
---

# Setup local do Eden no Mac

- .env gerado de `.env.example` (JWT, DataKey, hash admin; `$` do hash vira `$$` no .env para o compose). Senha do admin anotada como comentario no `.env` (gitignored).
- Docker Desktop estava parado: `open -a Docker`. Postgres via `docker compose up -d postgres`; Testcontainers (`dotnet test`) derrubou o container do compose — suba de novo depois dos testes.
- API: `dotnet run` com env `EDEN__Auth__*`/`EDEN__Security__DataKey` exportados; web: `npm run dev` (Node 26 funciona, DEV.md pede 22).
- 480 testes .NET ok, tsc/eslint/test:motion ok, contrato mobile ok.
- Bloqueio: mobile exige Dart ^3.13.5; Flutter local (stable, ~/Documents/Flutter) tem 3.12.2 e precisa de `flutter upgrade` (negado pelo classificador, decisão do Jonas). Ver [[icloud-evicta-node-modules-e-tsc-trava]] (flutter lento em ~/Documents).
- Ollama: qwen3:8b e nomic-embed-text já baixados, mas `ollama serve` precisa estar rodando.

## Atualizacao: Flutter
- `flutter upgrade` na pasta ~/Documents/Flutter travou (iCloud; ate `git status` pendura). Solucao: `git clone --depth 1 -b stable` em `~/dev/flutter` (3.47.6, Dart 3.13.5) e `export PATH=$HOME/dev/flutter/bin:$PATH`.
- Mobile: analyze limpo, 72 testes ok, 1 golden falha (spectrum_selected.png, 0.49% — provavel diferenca de render de texto entre plataformas; nao atualizado). Contrato ok.

## Commit, PR e Ollama
- PR https://github.com/jonasdantasviviani/Eden/pull/30 (golden spectrum_selected). Chat ponta a ponta ok: `POST /v1/chat` responde com qwen3:8b (~9 s a primeira), embeddings nomic 768 dim.
- `git status` travou >5 min no worktree em ~/Documents: iCloud tinha evictado ~1700 arquivos (`ls -lO` mostra `dataless`), disco com so 4,7 GB livres. Contorno: commit com plumbing (`hash-object -w`, `update-index --cacheinfo`, `write-tree`, `commit-tree`, `update-ref`) e PR via `gh api repos/{owner}/{repo}/pulls` (gh pr create roda git status e trava). Ver [[icloud-evicta-node-modules-e-tsc-trava]].

## Mudanca para ~/dev/Repos/Eden
- Repo movido de ~/Documents para ~/dev (git status 0,17 s). `.env` e node_modules nao vieram: refeitos (`.env` novo, `npm install`). `git worktree prune` limpou o worktree antigo.
- Volume `eden_pgdata` (de 02/10, usado antes pelo Jonas) tinha `postmaster.pid` vazio e o Postgres reiniciava em loop (disco cheio). Nao mexi nele: `COMPOSE_PROJECT_NAME=eden-local` no `.env` usa volume novo `eden-local_pgdata`.
- Disco ainda com ~3 GB livres; docker tem ~19 GB de build cache e ~7 GB de imagens reclamaveis.

## Limpeza de cache do Docker
- `docker builder prune -af` liberou 20,2 GB no Docker, mas o livre do Mac ficou em 3,2 GB: o Docker.raw do Docker Desktop nao encolhe na hora (reiniciar o Docker Desktop devolve). Imagens nao usadas (~7 GB) nao foram removidas por serem de outros projetos (BTech, HeavyOps).
