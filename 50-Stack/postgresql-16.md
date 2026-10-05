---
tipo: stack
titulo: PostgreSQL 16
projeto: [Heavy, ICook]
stack: [postgresql, npgsql]
tags: [tipo/stack, stack/postgresql]
palavras-chave: [postgres, postgresql, rls, row level security, particionamento, npgsql, supabase, banco, multi-inquilino]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# PostgreSQL 16
## Resumo
Banco padrao dos projetos novos; escolhido porque RLS resolve multi-inquilino e particionamento resolve o volume de ping.

## Detalhe

### Por que
- **RLS (Row Level Security)** resolve isolamento multi-inquilino no proprio banco
- **Particionamento** resolve a tabela de ping de localizacao em escala
- Npgsql e um dos melhores drivers de Postgres de qualquer ecossistema

### Roles do [[Heavy]] (`infra/sql/00-roles.sql`)
| Role | Uso |
| --- | --- |
| `heavyops_app` | a aplicacao — nao e dona das tabelas, **sem `BYPASSRLS`** |
| `heavyops_migration` | migrations |
| `postgres` | administracao — **nunca** em teste de isolamento |

### Ambiente local do Heavy
`docker compose -f infra/docker-compose.yml up -d` → postgres na porta **5433**, redis 6380, azurite 10000.

### No [[ICook]]
PostgreSQL 15 hospedado no **Supabase**, com EF Core 9 + Npgsql, e raw SQL para o match de ingredientes.

### Convencao
JSON e banco em `snake_case` via `UseSnakeCaseNamingConvention()`. Ver [[dominio-em-portugues-tecnico-em-ingles]].

## Relacionado
- [[multi-inquilino-com-rls-no-postgres]]
- [[rls-com-pool-de-conexoes-npgsql]]
- [[Heavy]]
