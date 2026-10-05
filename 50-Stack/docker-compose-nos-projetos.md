---
tipo: stack
titulo: Docker Compose nos projetos
projeto: [Heavy, BTech.NFe.Api, ICook]
stack: [docker]
tags: [tipo/stack, stack/docker]
palavras-chave: [docker, compose, container, sqlserver, postgres, redis, azurite, db-migrate, infra, ambiente local]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-11
confianca: alta
---

# Docker Compose nos projetos
## Resumo
Todo projeto sobe suas dependencias com `docker compose`; o que muda e o conjunto de servicos e o arquivo.

## Detalhe

| Projeto | Comando | Servicos |
| --- | --- | --- |
| [[Heavy]] | `docker compose -f infra/docker-compose.yml up -d` | postgres 16 (**5433**), redis 7 (**6380**), azurite (**10000**) |
| [[ICook]] | `docker-compose -f infra/docker-compose.yml up -d` | postgres, redis (perfil `full` inclui a API) |
| [[BTech.NFe.Api]] | `docker-compose.yml` na raiz | SQL Server 2022 Express, API, **`db-migrate`** |

### BTech.NFe.Api — particularidades
- Variantes por ambiente: `docker-compose.override.yml` (dev), `.dsv.yml`, `.hml.yml`, `.prd.yml`.
  As tres ultimas **complementam** o base (`-f docker-compose.yml -f docker-compose.prd.yml`), entao
  todo servico do base roda em prd tambem; o override so e aplicado sem `-f` → lugar certo para
  coisa so-local (ex.: `SEED_ADMIN=true`)
- O servico **`db-migrate`** aplica todo `migrations/NNN_*.sql` a cada `up`
  (ver [[migrations-sql-manuais-em-vez-de-ef-migrations]])
- `.bak` do painel ficam no volume nomeado `btech-backups`, montado em `/backups` na API e no
  SQL Server (antes era bind mount `./backups`). A API roda como `appuser`: o Dockerfile cria
  `/backups/uploads` com esse dono e o Docker copia a posse para o volume vazio na 1a montagem
- Testar mudancas de compose sem destruir o ambiente do Jonas: um arquivo extra com `name:`,
  `container_name` e `volumes.*.name` proprios + `SQLSERVER_PORT`/`API_PORT` no shell
  (sobrepoe o `.env`). No zsh, use funcao (`c() { docker compose -f ... "$@"; }`), nao `$C`
  — ver [[zsh-nao-separa-variavel-em-palavras]]
- "Banco nao sobe" no Mac: primeiro confira se o Docker Desktop esta aberto (`AutoStart` desligado);
  depois veja [[docker-do-banco-nao-sobe-no-mac]] (db-migrate falhando mudo, `AUTO_CLOSE`)

### Heavy — credenciais
As credenciais do Azurite no `docker-compose.yml` sao publicas e documentadas pela Microsoft —
**a unica excecao** a regra de nao commitar segredo. Nenhuma outra se aproveita dela.

## Relacionado
- [[BTech.NFe.Api]]
- [[Heavy]]
- [[ICook]]
