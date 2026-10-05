---
tipo: seguranca
titulo: 04. Ativar RLS
projeto: [todos]
stack: [postgresql]
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [rls, row level security, postgres, isolamento, tenant, multi-inquilino, policy, force, bypassrls, tabela nova, migration, schema, criar tabela, banco novo]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 04. Ativar RLS
## Resumo
Toda tabela com dado de cliente tem Row Level Security ativo, com `FORCE`, e a aplicacao conecta com role sem `BYPASSRLS`.

## Contexto
Regra 4 das 20 obrigatorias. No [[Heavy]] ela ja esta implementada e detalhada em
[[multi-inquilino-com-rls-no-postgres]] — leia essa nota antes de mexer em qualquer coisa de RLS.

## Detalhe

### O minimo
```sql
alter table ordem_servico enable row level security;
alter table ordem_servico force row level security;   -- vale ate para o dono da tabela
create policy p_tenant on ordem_servico
  using (transportadora_id = current_setting('app.transportadora_id')::uuid);
```

### As duas armadilhas que ja custaram caro
1. `set_config(..., false)` gruda o valor na conexao e o pool entrega para outro inquilino →
   [[rls-com-pool-de-conexoes-npgsql]]
2. Teste conectando como `postgres` ignora o RLS e passa mesmo quebrado →
   [[teste-de-isolamento-que-passa-quebrado]]

### Quando o banco nao tem RLS (SQL Server)
Nao ha equivalente direto barato. A defesa vira:
`HasQueryFilter` global no EF Core **em toda entidade** com dono/tenant, mais revisao de todo
SQL cru. E mais fragil — trate como divida, nao como solucao.
E o caso da [[BTech.NFe.Api]].

## Relacionado
- [[multi-inquilino-com-rls-no-postgres]]
- [[rls-com-pool-de-conexoes-npgsql]]
- [[07-travar-acesso-aos-registros]]
