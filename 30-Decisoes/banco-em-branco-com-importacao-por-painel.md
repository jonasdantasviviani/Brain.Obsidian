---
tipo: decisao
titulo: Ambiente local sobe com banco em branco; dados entram pelo painel de importacao
projeto: [BTech.NFe.Api]
stack: [docker, sqlserver, dotnet]
tags: [tipo/decisao, stack/docker, stack/sqlserver, empresa/btech]
palavras-chave: [banco em branco, banco vazio, schema base, bak, restore, importador, painel, administrador, seed, 123456, SEED_ADMIN, volume, btech-backups, bind mount, teste ponta a ponta, e2e]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Ambiente local sobe com banco em branco; dados entram pelo painel de importacao
## Resumo
Pedido do Jonas: o sistema sempre inicia em branco com um admin master, e o `.bak` do cliente e importado depois pelo painel — teste de ponta a ponta sem depender de banco pronto.

## Contexto
Antes, as migrations so alteravam um schema legado: era obrigatorio restaurar um `.bak` (script
`setup-local` ou SSMS a partir de `./backups` montado no SQL Server) antes do primeiro `up`.

## Detalhe

### O que foi escolhido
- `migrations/000_schema_base.sql`: schema completo, sem dados, gerado do banco real com SMO
  ([[schema-base-sqlserver-gerado-com-smo]]); no-op em qualquer banco que ja tenha schema.
- `migrations/015_seed_admin_local.sql`: `Administrador` / `123456`, AdminSistema + SuperUsuario,
  hash BCrypt pre-calculado (work factor 12, igual ao `PasswordService`).
- Bind mount `./backups:/backups` trocado por volume nomeado `btech-backups`.

### Alternativas descartadas
| Opcao | Por que nao |
| --- | --- |
| Tirar o `/backups` de vez, como pedido ao pe da letra | O restore do painel grava o `.bak` pela API e o `RESTORE` roda no container do SQL: sem pasta compartilhada o painel quebra. Volume interno atende "nao apontar para a pasta do host" |
| Schema do modelo EF (`dotnet ef dbcontext script`) | Contraria [[migrations-sql-manuais-em-vez-de-ef-migrations]] (o banco e a verdade) e perde trigger/procedure |
| Seed do admin sempre ligado | O `db-migrate` do compose base roda em dsv/hml/prd tambem; senha publica em producao. Gate por `SEED_ADMIN`, ligado so no override |
| Senha em texto puro no seed (o login legado aceita e migra) | Regra [[10-hash-nas-senhas]]: nunca em claro, nem em seed |

### Consequencias
- Tabelas de referencia fiscal (CST, CFOP, IBPT, CEST, Perfis/Permissoes, ParametrosSistema)
  ficam **vazias** no banco em branco, e o importador so traz Cadastros. Se algum fluxo E2E
  precisar delas, falta seed ou ampliar o importador.
- O `000` precisa ser regerado quando o schema mudar muito (senao banco novo ≠ banco migrado —
  as migrations `NNN` continuam cobrindo a diferenca, desde que idempotentes).
- Validado ponta a ponta em 2026-09-11: banco do zero → login → tenant → upload do `.bak` de
  34 MB → restore em `stg_import_*` → importacao de 794 registros, 0 erros → descarte.

## Relacionado
- [[BTech.NFe.Api]]
- [[schema-base-sqlserver-gerado-com-smo]]
- [[migrations-sql-manuais-em-vez-de-ef-migrations]]
- [[docker-compose-nos-projetos]]
- [[10-hash-nas-senhas]]
