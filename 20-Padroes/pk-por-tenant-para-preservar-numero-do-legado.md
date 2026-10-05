---
tipo: padrao
titulo: PK por tenant para preservar o número do legado na importação
projeto: [BTech.NFe.Api]
stack: [sqlserver, dotnet, efcore]
tags: [tipo/padrao, multi-tenant, importador, sqlserver, migracao]
palavras-chave: [chave primaria, primary key, IdTenant, multi-tenant, importacao, legado, SqlBulkCopy, KeepIdentity, preservar id, numero do pedido]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# PK por tenant para preservar o número do legado na importação

## Resumo
Ao trazer dados de um sistema que era "um banco por cliente" para um banco multi-tenant, pôr
`IdTenant` na **chave primária** e **preservar os IDs originais** sai mais barato e mais fiel do
que remapear chaves.

## Contexto
No [[BTech.NFe.Api]] a migração multi-tenant original só acrescentou a **coluna** `IdTenant`; as
PKs continuaram as do legado. Resultado: `CabPedidos.NumeroPedido`, `Empresas.CodEmpresa` e outras
24 chaves eram únicas no banco inteiro — dois clientes com o pedido nº 1 não cabiam juntos.

O importador contornava zerando a PK e remapeando, o que funciona para cadastro mas é inaceitável
para documento: número de nota e de pedido é dado que o cliente procura na tela.

## Detalhe

### A migração
Dinâmica, como a que criou a coluna: percorre `sys.key_constraints`, dropa a PK e recria com
`IdTenant` na frente. Sem FK para recriar (o schema Delphi não declarava nenhuma — conferir com
`SELECT COUNT(*) FROM sys.foreign_keys` antes de assumir).

`IdTenant` como **primeira** coluna: o índice clustered passa a agrupar fisicamente por tenant,
que é como toda query filtra.

Idempotente: pula o que já tem `IdTenant` na chave. Reconstrói o clustered de cada tabela, então
exige janela com a aplicação parada.

### A importação, depois disso
Com a chave delimitada por tenant, o ID original pode ser preservado — e aí **nada precisa ser
remapeado**: as ligações internas do legado (item → nota, item → pedido) continuam válidas de
graça.

`SqlBulkCopy` com `SqlBulkCopyOptions.KeepIdentity` copia a tabela inteira mantendo até as colunas
`IDENTITY`. O `IdTenant` entra como constante no próprio `SELECT` da origem
(`SELECT col1, col2, 7 AS IdTenant FROM tabela`), então o reader já chega pronto ao bulk.

Só as colunas presentes **nos dois lados** entram: o `.bak` de um cliente pode estar numa versão
do schema diferente da nossa.

Antes de copiar, apaga o que aquele tenant já tinha naquela tabela — reimportar passa a ser
idempotente em vez de violar a PK.

### A armadilha que vem junto
Mudar a PK do banco sem mudar a do modelo EF abre corrupção silenciosa no `UPDATE`. Ver
[[query-filter-nao-protege-update-entre-tenants]] — é obrigatório ler antes de aplicar este padrão.

## Relacionado
- [[query-filter-nao-protege-update-entre-tenants]]
- [[importador-mostra-o-que-nao-importou]]
- [[descartar-banco-temporario-no-fim-do-job]]
- [[BTech.NFe.Api]]
