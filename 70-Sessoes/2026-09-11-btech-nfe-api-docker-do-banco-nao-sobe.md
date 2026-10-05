---
tipo: sessao
titulo: BTech.NFe.Api — docker do banco nao subia localmente
projeto: [BTech.NFe.Api]
stack: [docker, sqlserver]
tags: [tipo/sessao, stack/docker, empresa/btech]
palavras-chave: [docker nao sobe, sqlserver, db-migrate, docker desktop parado, auto_close, ambiente local]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# BTech.NFe.Api — docker do banco nao subia localmente
## Resumo
Causa: Docker Desktop fechado; depois de aberto, stack subiu inteira (sqlserver, db-migrate, api healthy, /health 200).

## Contexto
Jonas relatou que o container do banco nao subia ao rodar o projeto local.

## Detalhe
- Daemon parado: contexto `desktop-linux`, Docker Desktop instalado (4.85) mas fechado, `AutoStart: false`; Colima tambem parado.
- `open -a Docker` → `btech-sqlserver` voltou sozinho e ficou `healthy` (volume `btech-sqlserver-data` intacto).
- `db-migrate` falhou mudo (`rc=1` sem saida) nas primeiras execucoes e passou depois; suspeita em `AUTO_CLOSE = 1` no `BTechPLUSTESTE`. Detalhes e comandos em [[docker-do-banco-nao-sobe-no-mac]].
- `docker compose up -d --build` final: migrate exit 0, API healthy, `GET /health` 200.
- Nada foi alterado no repositorio nem no banco. Pendencias sugeridas: `AUTO_CLOSE OFF`, `platform: linux/amd64` no compose, ligar autostart do Docker Desktop.

## Relacionado
- [[BTech.NFe.Api]]
- [[docker-do-banco-nao-sobe-no-mac]]
- [[docker-compose-nos-projetos]]
