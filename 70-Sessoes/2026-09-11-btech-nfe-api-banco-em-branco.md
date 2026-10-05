---
tipo: sessao
titulo: BTech.NFe.Api — ambiente local com banco em branco e admin master
projeto: [BTech.NFe.Api]
stack: [docker, sqlserver, dotnet]
tags: [tipo/sessao, stack/docker, stack/sqlserver, empresa/btech]
palavras-chave: [banco em branco, schema base, 000_schema_base, seed admin, Administrador, 123456, SEED_ADMIN, btech-backups, volume, importador, painel, bak]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# BTech.NFe.Api — ambiente local com banco em branco e admin master
## Resumo
Stack local passou a subir com banco em branco + `Administrador`/`123456`; `.bak` so via painel; bind mount `./backups` virou volume `btech-backups`.

## Contexto
Pedido do Jonas logo apos [[2026-09-11-btech-nfe-api-docker-do-banco-nao-sobe]]. Decisao e
trade-offs em [[banco-em-branco-com-importacao-por-painel]].

## Detalhe
Arquivos alterados (nao commitados — o Jonas tinha trabalho do webhook Focus pendente no mesmo
working tree, intocado):
- novos: `migrations/000_schema_base.sql` (5977 linhas, SMO), `migrations/015_seed_admin_local.sql`
- `docker-compose.yml`: `db-migrate` com laco sobre `NNN_*.sql`, `CREATE DATABASE ... COLLATE
  Latin1_General_CI_AS` + `AUTO_CLOSE OFF`, `SEED_ADMIN: "false"`; volume `btech-backups`
- `docker-compose.override.yml`: `SEED_ADMIN: "true"`
- `Dockerfile`: `/backups/uploads` com dono `appuser`
- `scripts/setup-local.{sh,ps1}`: so sobem a stack e esperam a API
- `README.md`, `INSTALLATION.md`, `CLAUDE.md` e 2 comentarios em C#: removida a doc das rotas
  `admin/database/*` que nao existem mais e do restore manual via `./backups`

Verificado num projeto Compose isolado (`e2e-*`, portas 14330/15204): banco do zero, sem override
(0 usuarios), segunda subida (nada recriado), `000` contra o banco legado (no-op), fluxo completo
do painel (794 registros, 0 erros). Ambiente de teste removido depois.

Pendente com o Jonas: o volume `btech-sqlserver-data` dele ainda tem o legado restaurado; para
ficar em branco precisa `docker compose down -v` (destrutivo, pedi confirmacao).
Achado de seguranca fora do escopo (virou tarefa separada, **resolvida no mesmo dia** em
[[2026-09-11-btech-nfe-api-importador-sem-vazar-credencial]]): `restaurar` devolvia connection
string com senha do `sa` ao navegador e `preview`/`iniciar` aceitavam connection string arbitraria
([[15-nao-vazar-dados]], [[23-prevenir-ssrf]]).
Upload de 34 MB voltou 400 uma unica vez (5,1 s, `ValidationProblemDetails`) logo apos a API subir;
nao reproduziu em 4 tentativas seguintes.

## Relacionado
- [[BTech.NFe.Api]]
- [[banco-em-branco-com-importacao-por-painel]]
- [[schema-base-sqlserver-gerado-com-smo]]
- [[zsh-nao-separa-variavel-em-palavras]]
- [[docker-do-banco-nao-sobe-no-mac]]
