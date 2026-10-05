---
tipo: seguranca
titulo: 13. Queries parametrizadas
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [sql injection, query, parametrizada, concatenacao, interpolacao, orm, fromsqlraw, dapper, prepared statement, consulta, buscar, listar, filtro, relatorio, select, banco]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 13. Queries parametrizadas
## Resumo
Nenhum valor vindo do usuario entra em SQL por concatenacao ou interpolacao de string. Sempre parametro.

## Contexto
Regra 13 das 20 obrigatorias.

## Detalhe

### O erro e a correcao em .NET
```csharp
// ERRADO — interpolacao vira SQL injection
ctx.Notas.FromSqlRaw($"select * from nota where cliente = '{cliente}'");

// CERTO — FromSqlInterpolated parametriza a interpolacao
ctx.Notas.FromSqlInterpolated($"select * from nota where cliente = {cliente}");

// CERTO — parametro explicito
ctx.Notas.FromSqlRaw("select * from nota where cliente = @c",
                     new SqlParameter("@c", cliente));
```

`FromSqlRaw` com `$"..."` e a armadilha: parece igual ao seguro, e nao e.
`FromSqlInterpolated` **parametriza**; `FromSqlRaw` com interpolacao **concatena**.

### O que a auditoria procura
`FromSqlRaw($"` · `ExecuteSqlRaw($"` · `"SELECT ..." + variavel` · template string com `${}` em
query JS · f-string com SQL em Python.

### O que nao protege
- Escapar aspas manualmente
- Validar o input (ajuda, mas nao substitui — [[14-validacao-dos-inputs]] e camada diferente)
- ORM: protege por padrao, **mas so enquanto voce nao usa a saida de emergencia** (`Raw`)

### Nomes de coluna e tabela nao sao parametrizaveis
Se a query monta `ORDER BY {campo}` a partir do usuario, use **allowlist**:
`campo switch { "nome" => "nome", "data" => "criado_em", _ => "id" }`.
E o caso mais comum de injection que sobra depois que todo mundo ja usa parametro.

### No seu codigo hoje
[[BTech.NFe.Api]] e [[Heavy]] passam limpo: acesso por EF Core, sem SQL cru concatenado.

## Relacionado
- [[14-validacao-dos-inputs]]
- [[04-ativar-rls]]
