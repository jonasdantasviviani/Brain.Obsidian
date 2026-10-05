---
tipo: armadilha
titulo: dotnet test derruba o Postgres do docker compose
projeto: [Eden]
stack: [docker, dotnet]
tags: [tipo/armadilha, stack/docker]
palavras-chave: [testcontainers, ryuk, connection refused 5432, compose]
origem: claude-code
criado: 2026-10-04
atualizado: 2026-10-04
confianca: media
---

## Resumo
Depois de `dotnet test`, o container postgres do compose sumiu e a API deu "Connection refused 5432".

## Contexto
Testcontainers (Ryuk) rodou com o compose ativo no Docker Desktop do Mac.

## Detalhe
Solucao: rodar os testes antes de subir o banco de dev, ou `docker compose up -d postgres` de novo depois. Causa exata nao confirmada.

## Relacionado
[[Eden]]
