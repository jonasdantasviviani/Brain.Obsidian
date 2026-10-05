---
tipo: padrao
titulo: Multi-inquilino com RLS no PostgreSQL — as 4 regras
projeto: [Heavy]
stack: [postgresql, dotnet, efcore, npgsql]
tags: [tipo/padrao, stack/postgresql, stack/dotnet, cerebro/padrao-obrigatorio, seguranca]
palavras-chave: [multi-inquilino, multitenant, rls, row level security, postgres, isolamento, tenant, transportadora, set_config, npgsql, pool, hasqueryfilter]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Multi-inquilino com RLS no PostgreSQL — as 4 regras
## Resumo
Quatro regras nao-negociaveis para isolar dados por inquilino; violar qualquer uma vaza dado entre clientes em silencio.

## Contexto
Definido no [[Heavy]] (doc 27, §4). Vale para qualquer produto multi-inquilino em Postgres.
O modo de falha e o pior possivel: **silencioso e so sob concorrencia**.

## Detalhe

### 1. `set_config(..., true)` dentro de transacao explicita, sempre
```csharp
await using var tx = await conexao.BeginTransactionAsync(ct);
await conexao.ExecuteAsync("select set_config('app.transportadora_id', @id, true)", ...);
```
Sem o `true` (equivalente a `LOCAL`), o valor **gruda na conexao** e o pool do Npgsql entrega
essa conexao para a requisicao de outro inquilino. Ver [[rls-com-pool-de-conexoes-npgsql]].

### 2. Quem abre a transacao e o middleware de inquilino, nunca o endpoint
Se cada endpoint precisar lembrar, um vai esquecer.

### 3. Filtro global do EF Core como segunda camada
`HasQueryFilter` em toda entidade com `TransportadoraId`.
O RLS cobre o SQL cru que o EF nao ve; o filtro cobre o caso do middleware falhar.
**Nenhuma das duas sozinha basta.**

### 4. A aplicacao conecta como role sem privilegio
`heavyops_app` nao e dona das tabelas e nao tem `BYPASSRLS`.
Migration usa `heavyops_migration`.
Se um teste conectar como `postgres`, o RLS e ignorado e **o teste de isolamento passa mesmo
quebrado**.

### Workers
Worker nao tem inquilino no token: abre o contexto **por job**, e nunca processa
transportadoras diferentes na mesma transacao.

## Relacionado
- [[Heavy]]
- [[rls-com-pool-de-conexoes-npgsql]]
- [[teste-de-isolamento-que-passa-quebrado]]
