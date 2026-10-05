---
tipo: padrao
titulo: Script de schema base do SQL Server gerado com SMO (idempotente e atomico)
projeto: [BTech.NFe.Api]
stack: [sqlserver, dotnet, docker]
tags: [tipo/padrao, stack/sqlserver, stack/dotnet]
palavras-chave: [smo, sqlmanagementobjects, gerar ddl, script schema, schema only, banco vazio, noexec, set noexec on, transacao ddl, xact_abort, dotnet run arquivo, file-based app, sqlcmd, collation, crlf]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Script de schema base do SQL Server gerado com SMO (idempotente e atomico)
## Resumo
Gerar o DDL de um banco existente com SMO via app de arquivo unico do .NET 10, e embrulhar num script que so executa em banco vazio e cria tudo-ou-nada.

## Contexto
Usado no [[BTech.NFe.Api]] para o `migrations/000_schema_base.sql`
([[banco-em-branco-com-importacao-por-painel]]). Serve para qualquer projeto sobre SQL Server
legado que precise subir um banco vazio sem `.bak`.

## Detalhe

### 1. Gerar o DDL — .NET 10 file-based app (sem csproj)
```csharp
#:package Microsoft.SqlServer.SqlManagementObjects@181.36.0
using Microsoft.SqlServer.Management.Smo; // + Common, Microsoft.Data.SqlClient
var db = new Server(new ServerConnection(new SqlConnection(cs))).Databases[nome];
var opt = new ScriptingOptions { NoCollation = true, AnsiPadding = true, SchemaQualify = true,
  DriPrimaryKey = true, DriDefaults = true, DriChecks = true, DriUniqueKeys = true,
  Indexes = true, Triggers = true, DriForeignKeys = false };
// passo 1: tabelas (t.Script(opt)) | passo 2: t.ForeignKeys | passo 3: StoredProcedures
```
`SA_PASSWORD=... dotnet run dump.cs -- <banco> <saida.sql>` — senha por variavel de ambiente.
FKs num passo separado evitam ordenar tabelas por dependencia. Filtrar `Schema == "dbo"` deixa
de fora schemas que a propria lib cria (ex.: `[HangFire]`).

### 2. Embrulhar
```sql
IF EXISTS (SELECT 1 FROM sys.tables WHERE schema_id = SCHEMA_ID('dbo'))
BEGIN
    PRINT 'Schema dbo ja existe — pulando.';
    SET NOEXEC ON;          -- os batches seguintes so compilam, nao executam
END
GO
SET XACT_ABORT ON;
BEGIN TRANSACTION;          -- DDL e transacional no SQL Server e atravessa GO na mesma sessao
GO
-- ... DDL gerado ...
COMMIT TRANSACTION;
GO
SET NOEXEC OFF;
GO
```
Com `sqlcmd -b`, qualquer erro encerra a sessao → rollback → a proxima execucao tenta de novo
(o banco nunca fica "meio criado" e por isso pulado para sempre).

### 3. Cuidados
- `NoCollation = true` so se todas as colunas usam a collation do banco — confira com
  `SELECT collation_name, COUNT(*) FROM sys.columns ... GROUP BY collation_name` e crie o banco
  com essa collation (`CREATE DATABASE ... COLLATE Latin1_General_CI_AS`). O padrao do servidor
  no container e `SQL_Latin1_General_CP1_CI_AS`, diferente do legado.
- Normalizar fim de linha (`tr -d '\r'`) antes de commitar — ver [[repositorio-oscilando-entre-crlf-e-lf]].
- Gerar a partir do banco (verdade do schema), nao do modelo EF: o modelo nao traz trigger nem
  procedure.
- sqlcmd sem `-I` roda com `QUOTED_IDENTIFIER OFF`; o SMO ja emite `SET QUOTED_IDENTIFIER ON`.

## Relacionado
- [[banco-em-branco-com-importacao-por-painel]]
- [[migrations-sql-manuais-em-vez-de-ef-migrations]]
- [[BTech.NFe.Api]]
