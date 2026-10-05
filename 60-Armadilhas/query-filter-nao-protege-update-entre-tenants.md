---
tipo: armadilha
titulo: "Query filter do EF não entra no UPDATE — alterar um tenant corrompia o outro"
projeto: [BTech.NFe.Api]
stack: [dotnet, efcore, sqlserver, multi-tenant]
tags: [tipo/armadilha, efcore, multi-tenant, seguranca, dados]
palavras-chave: [query filter, HasQueryFilter, multi-tenant, IdTenant, update, delete, chave primaria, concurrency token, IsConcurrencyToken, corrupcao de dados, vazamento entre clientes]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Query filter do EF não entra no UPDATE — alterar um tenant corrompia o outro

## Resumo
`HasQueryFilter` protege **SELECT**. O `UPDATE` e o `DELETE` que o EF gera usam a **chave do
modelo**. Se a chave do modelo não tem `IdTenant`, alterar uma linha de um cliente altera a linha
de mesmo número de **todos** os outros — sem erro, sem log, sem sintoma.

## Contexto
No [[BTech.NFe.Api]] a migração `020` pôs `IdTenant` na PK do **banco** (127 tabelas), para poder
importar preservando o número de pedido/empresa do legado. O modelo do EF ficou como estava — a
chave do legado, sem `IdTenant` — por decisão consciente: transformar 136 entidades em chave
composta quebraria `GetByIdAsync`, controllers e telas.

Parecia seguro, com o argumento "o query filter garante um tenant por contexto". **Não garante.**

## Detalhe

### O que o teste mostrou
Dois tenants com o pedido nº 1. Alterar o status do pedido do tenant A:

```csharp
var pedido = await ctxA.Set<CabPedido>().FirstAsync(p => p.NumeroPedido == 1);
pedido.Status = "Fechado";
await ctxA.SaveChangesAsync();
```

O EF gerou `UPDATE CabPedidos SET Status=... WHERE NumeroPedido = 1` — **sem IdTenant**. O pedido
do tenant B virou "Fechado" junto. O `SELECT` tinha o filtro; o `UPDATE` não.

### A correção — uma linha
```csharp
modelBuilder.Entity<TEntity>().Property(e => e.IdTenant).IsConcurrencyToken();
```

Concurrency token entra no `WHERE` de todo `UPDATE` e `DELETE`. Resolve sem virar chave composta,
e não dá falso positivo de concorrência: o `IdTenant` nunca muda, e o filtro já trouxe a linha do
tenant certo, então o WHERE sempre casa.

Aplicado no mesmo laço por reflexão que já aplicava o query filter às entidades `ITenantScoped` —
uma linha cobre as 136.

### Por que passou despercebido até aqui
Só aparece com **dois tenants tendo a mesma chave do legado**. Enquanto o sistema rodava com um
cliente por banco, ou com chaves IDENTITY globais (que nunca repetem entre tenants), o bug
existia e não tinha como se manifestar. A migração 020 é que criou a condição.

## A outra ponta da mesma inversão
Mudar a chave inverte premissas que o código escreveu por escrito. No mesmo repositório,
`ProximoCodigoAsync` usava `IgnoreQueryFilters()` **de propósito**, com o comentário "a PK é única
no banco inteiro, então reaproveitar um número de outro tenant colidiria" — verdade antes da 020.
Depois dela não há colisão possível, e o máximo global virou o bug: a numeração de cada cliente
herdaria a do maior (importar um cliente com pedidos até 50.000 empurraria o próximo pedido de
todos para 50.001).

Ao mudar uma chave, procure por `IgnoreQueryFilters`, `AsNoTracking().Max`, sequências e
contadores: cada um deles foi escrito sob a premissa antiga.

## Como evitar
Multi-tenant por query filter **não é isolamento**. Sempre que a chave do modelo não contiver o
discriminador de tenant, teste explicitamente: dois tenants, mesma chave, `UPDATE` num e
conferência no outro. É um teste de 15 linhas que pega corrupção silenciosa de dados de cliente.

## Relacionado
- [[pk-por-tenant-para-preservar-numero-do-legado]]
- [[importador-mostra-o-que-nao-importou]]
- [[BTech.NFe.Api]]
