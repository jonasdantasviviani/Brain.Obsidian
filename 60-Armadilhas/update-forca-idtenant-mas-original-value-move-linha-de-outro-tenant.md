---
tipo: armadilha
titulo: "IdTenant forçado só no valor atual: UPDATE movia a linha de outro tenant para o meu"
projeto: [BTech.NFe.Api]
stack: [efcore, sqlserver, multi-tenant]
tags: [tipo/armadilha, multi-tenant, efcore, seguranca]
palavras-chave: [IsConcurrencyToken, OriginalValue, PreencherIdTenant, attach, update entre tenants, testcontainers]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# IdTenant forçado só no valor atual move a linha de outro tenant

## Resumo
`PreencherIdTenant` gravava o tenant atual em entidade `Modified`, mas o concurrency token usa o valor
**original** no WHERE. Entidade anexada com `IdTenant` alheio gerava `SET IdTenant=meu WHERE chave AND IdTenant=alheio`.
Correção: `entry.Property("IdTenant").OriginalValue = tenantAtual`. Complementa [[query-filter-nao-protege-update-entre-tenants]].
Achado pelo teste em SQL Server real (`tests/BTech.NFe.Tests.SqlServer`, PR 61), que o InMemory não pegaria.

## Dívida achada junto
Modelo x schema: `ParametrosBoleto.DescricaoOBS`, `ParametrosSistema.DataExpira3` e 23 colunas de `ProdutosXfatura` não existem no schema da 000+migrações.
