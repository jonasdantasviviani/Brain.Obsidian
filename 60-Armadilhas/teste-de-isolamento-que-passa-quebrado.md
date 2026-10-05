---
tipo: armadilha
titulo: Teste de isolamento multi-inquilino que passa mesmo quebrado
projeto: [Heavy]
stack: [postgresql, dotnet]
tags: [tipo/armadilha, stack/postgresql, seguranca, cerebro/critico]
palavras-chave: [teste, isolamento, rls, postgres, bypassrls, falso positivo, role, conexao, integracao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Teste de isolamento multi-inquilino que passa mesmo quebrado
## Resumo
Se o teste conectar como `postgres`, o RLS e ignorado e o teste de isolamento passa mesmo com o isolamento quebrado.

## Contexto
Regra 4 de [[multi-inquilino-com-rls-no-postgres]], no [[Heavy]].

## Detalhe

### Por que acontece
O superusuario `postgres` — e qualquer role com `BYPASSRLS` ou dona da tabela — **ignora as
policies de RLS**. Um teste que conecta assim nunca exercita o mecanismo que deveria validar.

O resultado e o pior tipo de teste: **verde e inutil**. Ele da confianca de que o isolamento
funciona, enquanto em producao a aplicacao vaza.

### A correcao
O teste de integracao deve conectar com a **mesma role da aplicacao** (`heavyops_app`), que:
- nao e dona das tabelas
- nao tem `BYPASSRLS`

Migration usa `heavyops_migration`. `postgres` so para administracao.
As roles estao em `infra/sql/00-roles.sql` — ler antes de mexer no RLS.

### Como validar que o teste presta
Quebre o isolamento de proposito (remova o `set_config`) e confirme que o teste **falha**.
Se continuar verde, o teste esta conectando com a role errada.

O Heavy tem esse teste: commit `025826d test(integracao): isolamento multi-inquilino contra
Postgres real`.

## Relacionado
- [[multi-inquilino-com-rls-no-postgres]]
- [[rls-com-pool-de-conexoes-npgsql]]
- [[postgresql-16]]
