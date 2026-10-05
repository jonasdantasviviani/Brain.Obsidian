---
tipo: projeto
titulo: Eden
projeto: [Eden]
stack: [dotnet, nextjs, postgres, flutter, docker]
tags: [tipo/projeto, stack/dotnet, stack/nextjs]
palavras-chave: [eden, assistente pessoal, make up, dotnet test, flutter]
origem: claude-code
criado: 2026-10-04
atualizado: 2026-10-04
confianca: alta
---

## Resumo
Assistente pessoal: API .NET 10 + web Next.js + app Flutter + Postgres/pgvector + Ollama local.

## Contexto
Repo em ~/Documents/Repos/Eden. Guia: docs/DEV.md.

## Detalhe
- Setup: `cp .env.example .env` (JWT, DataKey, hash via `dotnet run --project src/Eden.Api -- hash-password`; `$` vira `$$` no .env do compose), `make db`, API com env `EDEN__*` exportado, `cd web && npm install && npm run dev`.
- Testes: `dotnet test` (480 ok, usa Testcontainers), `npm run typecheck`, `npm run lint`, `npm run test:motion`, `make mobile-test`.
- Mobile exige Dart ^3.13.5 (Flutter stable atualizado).
- Ollama: qwen3:8b + nomic-embed-text; sem `ollama serve` o agente fica em modo basico.

## Relacionado
[[2026-10-04-eden-setup-local-mac]] [[testcontainers-derruba-postgres-do-compose]]
