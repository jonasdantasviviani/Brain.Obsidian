---
tipo: armadilha
titulo: "IDENTITY é contador da tabela inteira: em multi-tenant o código pula (69 → 1093796)"
projeto: [BTech.NFe.Api]
stack: [sqlserver, efcore, dotnet]
tags: [tipo/armadilha, multi-tenant, sqlserver]
palavras-chave: [identity, identity_insert, codigo sequencial, 1093796, proximo codigo, clientes, fornecedores, vendedores, KeepIdentity, importacao]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# IDENTITY é contador da tabela inteira: em multi-tenant o código pula

## Resumo
Depois da [[pk-por-tenant-para-preservar-numero-do-legado]] a PK é (IdTenant + código), mas o
`IDENTITY` continua um contador só para a tabela. Importação com `KeepIdentity` e outros tenants
empurram o contador: o cliente depois do 69 saiu **1093796**.

## Correção
`IRepository.AdicionarComCodigoSequencialAsync(entity, e => e.CodCliente)`: maior do tenant + 1,
`SET IDENTITY_INSERT ... ON` numa transação (o SET vale para a sessão), nova tentativa em
2627/2601. Usado por Cliente, Fornecedor, Vendedor, Produto, Grupo, TiposPagto.

## Pegadinhas
- `ExecuteSqlRawAsync` com string concatenada = erro EF1003 no build; montar o comando numa
  variável e escapar o identificador (`]` → `]]`).
- Provider InMemory dos testes usa a chave do MODELO (só o código): lá a sequência por tenant
  colide entre tenants — `ProximoCodigoAsync` usa o máximo global quando `!IsSqlServer()`.

## Relacionado
- [[BTech.NFe.Api]]
- [[pk-por-tenant-para-preservar-numero-do-legado]]
- [[repositorio-generico-e-servicebase]]
- [[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]]
