---
tipo: armadilha
titulo: db-migrate não roda de novo no docker compose up -d depois de já ter concluído
projeto: [BTech.NFe.Api]
stack: [docker, sqlserver]
tags: [tipo/armadilha, stack/docker]
palavras-chave: [db-migrate, docker compose up, service_completed_successfully, migracao nao aplicada, force-recreate, one-shot]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# db-migrate não roda de novo no `docker compose up -d`
## Resumo
Depois de mergear a migração 016, `docker compose up -d --build` reconstruiu e subiu a API, mas o serviço `db-migrate` continuou como **"Exited (0) 28 hours ago"** — a migração nova não foi aplicada.

## Contexto
[[BTech.NFe.Api]], 2026-09-12. O CLAUDE.md do repo diz que o db-migrate aplica os scripts "a cada `up`"; na prática isso não aconteceu com o container de um dia antes.

## Detalhe
- Causa provável: a API depende do db-migrate com `service_completed_successfully`, e o compose
  considera a dependência já satisfeita pelo container antigo que saiu com 0 (não confirmado na
  documentação — o sintoma e a correção sim).
- Correção que funcionou:
  ```bash
  docker compose -f docker-compose.yml -f docker-compose.override.yml up -d --force-recreate db-migrate
  docker logs <container-db-migrate> | grep "Aplicando migração"
  ```
- Conferir sempre com `docker compose ps -a`: o horário do "Exited" tem que ser de agora.
- Pendência: corrigir a frase do CLAUDE.md da API (ou o compose) — ainda não feito.

## Relacionado
- [[BTech.NFe.Api]]
- [[banco-em-branco-com-importacao-por-painel]]
