---
tipo: armadilha
titulo: Docker do banco nao sobe no Mac (daemon parado, db-migrate falhando mudo)
projeto: [BTech.NFe.Api]
stack: [docker, sqlserver]
tags: [tipo/armadilha, stack/docker, stack/sqlserver, empresa/btech]
palavras-chave: [docker nao sobe, banco nao sobe, cannot connect to the docker daemon, docker desktop, colima, autostart, db-migrate, sqlcmd rc=1, falha silenciosa, auto_close, sql server express, rosetta, amd64, arm64, apple silicon, btech-sqlserver]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Docker do banco nao sobe no Mac (daemon parado, db-migrate falhando mudo)
## Resumo
No Mac do Jonas, "o docker do banco nao sobe" foi o Docker Desktop fechado; depois de aberto, o `db-migrate` ainda pode falhar mudo nos primeiros minutos.

## Contexto
`BTech.NFe.Api` no macOS arm64 (Apple Silicon, 16 GB). Dois runtimes instalados: Docker Desktop
(contexto ativo `desktop-linux`) e Colima (contexto `colima`). Docker Desktop esta com
`AutoStart: false`, entao depois de reiniciar o Mac nada sobe sozinho.

## Detalhe

### 1. Primeiro checar o daemon
```bash
docker info >/dev/null 2>&1 && echo ok || echo "daemon parado"
docker context ls          # qual contexto esta ativo
open -a Docker             # sobe o Docker Desktop (~5 s)
```
Erro tipico: `Cannot connect to the Docker daemon at unix:///Users/jonasviviani/.docker/run/docker.sock`.
Os containers tem `restart: unless-stopped`, entao `btech-sqlserver` volta sozinho quando o daemon sobe.

### 2. Depois, o db-migrate pode falhar sem mensagem
Logo apos o SQL Server subir, o `db-migrate` saiu com exit 1 — cada `sqlcmd` voltava `rc=1`
com **zero bytes de saida**, em arquivos diferentes a cada execucao (001 numa vez, 002-014 na outra).
Minutos depois, as mesmas 14 migrations passaram 42/42 e o `docker compose up -d --build` completo subiu.
Como a API depende de `db-migrate: service_completed_successfully`, parece que "nada sobe".

Suspeita principal (nao provada): o `BTechPLUSTESTE` esta com **`AUTO_CLOSE = 1`** (veio do `.bak`
legado de SQL Express). O errorlog mostra `Starting up database 'BTechPLUSTESTE'` a cada conexao,
e as falhas coincidiram com esse abre/fecha, tudo emulado (imagem `amd64` via Rosetta).

```bash
# conferir
docker exec btech-sqlserver bash -c '/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$SA_PASSWORD" -No -Q "SELECT name,is_auto_close_on FROM sys.databases"'
# log interno (o docker logs nao mostra tudo)
docker exec btech-sqlserver grep -a "Starting up database" /var/opt/mssql/log/errorlog | tail
# rodar migrations isoladas, com codigo de saida por arquivo
docker compose run --rm --no-deps --entrypoint /bin/bash db-migrate -c 'for f in /migrations/0*.sql; do /opt/mssql-tools18/bin/sqlcmd -S sqlserver -U sa -P "$SA_PASSWORD" -d "$DB_NAME" -b -No -i "$f" >/dev/null 2>&1; echo "$(basename $f) rc=$?"; done'
```
Se falhar de novo: rodar `docker compose up -d` outra vez resolve na pratica.

**Confirmado em 2026-09-11**: o SQL Server **Express cria todo banco novo com `AUTO_CLOSE = 1`**
(mesmo com o `model` mostrando 0) — nao era so heranca do `.bak`. O `db-migrate` agora cria o
banco com `ALTER DATABASE ... SET AUTO_CLOSE OFF` logo apos o `CREATE DATABASE`.

### Pegadinha de leitura de log
`docker logs btech-db-migrate` **acumula** as execucoes anteriores do mesmo container; a saida
"✔ Migrações concluídas" pode ser de dias atras. Use `docker logs --since 5m -t`.

## Relacionado
- [[BTech.NFe.Api]]
- [[docker-compose-nos-projetos]]
- [[migrations-sql-manuais-em-vez-de-ef-migrations]]
