---
tipo: armadilha
titulo: RLS vaza entre inquilinos com pool de conexoes do Npgsql
projeto: [Heavy]
stack: [postgresql, npgsql, dotnet]
tags: [tipo/armadilha, stack/postgresql, seguranca, cerebro/critico]
palavras-chave: [rls, pool, npgsql, set_config, local, vazamento, multi-inquilino, tenant, conexao, transacao, postgres]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# RLS vaza entre inquilinos com pool de conexoes do Npgsql
## Resumo
`set_config('app.tenant_id', v, false)` gruda o valor na conexao; o pool entrega essa conexao para outro inquilino.

## Contexto
Chamada de "armadilha critica" na documentacao do [[Heavy]] (doc 27). O modo de falha e o pior
possivel: **silencioso, e so acontece sob concorrencia** — passa em todos os testes sequenciais.

## Detalhe

### O erro
```csharp
// ERRADO — o terceiro argumento false faz o valor persistir na conexao
await conexao.ExecuteAsync("select set_config('app.transportadora_id', @id, false)", ...);
```
O Npgsql devolve a conexao ao pool com a variavel ainda setada. A proxima requisicao — de
**outra transportadora** — pega essa conexao e enxerga os dados do inquilino anterior.

### A correcao
```csharp
await using var tx = await conexao.BeginTransactionAsync(ct);
// true == LOCAL: o valor morre no fim da transacao
await conexao.ExecuteAsync("select set_config('app.transportadora_id', @id, true)", ...);
```
Duas condicoes juntas: **`true`** (equivalente a `LOCAL`) **e** dentro de **transacao explicita**.

### Defesa em profundidade
Nao confie so nisso. As outras tres regras de [[multi-inquilino-com-rls-no-postgres]] existem
justamente porque essa aqui e facil de errar:
middleware abre a transacao (nao o endpoint) · `HasQueryFilter` no EF Core como segunda camada ·
aplicacao conecta com role sem `BYPASSRLS`.

### Worker
Worker nao tem inquilino no token: abre o contexto **por job** e nunca processa transportadoras
diferentes na mesma transacao.

## Relacionado
- [[multi-inquilino-com-rls-no-postgres]]
- [[teste-de-isolamento-que-passa-quebrado]]
- [[Heavy]]
