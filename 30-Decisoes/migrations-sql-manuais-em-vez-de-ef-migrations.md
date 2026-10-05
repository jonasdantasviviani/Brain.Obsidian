---
tipo: decisao
titulo: Migrations SQL manuais na BTech.NFe.Api (nao usar dotnet ef migrations)
projeto: [BTech.NFe.Api]
stack: [dotnet, efcore, sqlserver]
tags: [tipo/decisao, stack/dotnet, stack/sqlserver]
palavras-chave: [migration, migrations, efcore, scaffold, sql, schema, banco legado, db-migrate, docker]
origem: claude-code
criado: 2026-01-01
atualizado: 2026-09-11
confianca: alta
---

# Migrations SQL manuais na BTech.NFe.Api (nao usar dotnet ef migrations)
## Resumo
O modelo EF Core da BTech.NFe.Api e scaffolded de um banco existente; evolucao de schema e por script SQL, nunca por `dotnet ef migrations add`.

## Contexto
[[BTech.NFe.Api]] roda sobre um SQL Server legado ja em producao. Contrasta com o [[ICook]], onde
**toda** mudanca de schema e via EF Core migration.

## Detalhe

### Como funciona aqui
- Scripts SQL ficam em `migrations/` na raiz do repositorio
- O servico `db-migrate` do Docker Compose aplica **todo** `NNN_*.sql` em ordem, a cada `up`
  (laco no entrypoint desde 2026-09-11) — entao toda migration precisa ser idempotente
- `000_schema_base.sql` cria o schema inteiro num banco vazio e e no-op nos demais
  (ver [[schema-base-sqlserver-gerado-com-smo]])
- Quando o schema muda: nova migration `NNN` (altera bancos existentes) e/ou **re-scaffoldar**
  as entidades; o `000` pode ser regerado com SMO de um banco com tudo aplicado

### O que nao fazer
`dotnet ef migrations add` neste repositorio. O modelo nao e a fonte da verdade do schema — o
banco e. Pelo mesmo motivo, o `000` foi gerado do banco (SMO), nao do modelo
(`dotnet ef dbcontext script`): o modelo nao carrega o trigger `Audita_Produtos` nem a procedure.

## Relacionado
- [[BTech.NFe.Api]]
- [[ICook]]
- [[repositorio-generico-e-servicebase]]
