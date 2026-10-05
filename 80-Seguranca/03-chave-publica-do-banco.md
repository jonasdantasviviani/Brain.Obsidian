---
tipo: seguranca
titulo: 03. So chave publica do banco no cliente
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [supabase, anon key, service role, chave publica, cliente, next_public, firebase, banco, exposicao, app mobile, conectar banco do app]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 03. So chave publica do banco no cliente
## Resumo
No cliente so entra a chave publica/anon do banco, e ela so e segura se o RLS estiver ativo.

## Contexto
Regra 3 das 20 obrigatorias. Critica em stacks tipo Supabase/Firebase, onde o app fala direto
com o banco.

## Detalhe

### A regra
| Chave | Onde pode estar |
| --- | --- |
| `anon` / `publishable` | cliente (web, mobile) — **desde que haja RLS** |
| `service_role` / `secret` | **somente no servidor**, nunca em bundle |

### O que a auditoria procura
- `NEXT_PUBLIC_*` cujo nome contenha SERVICE, SECRET, PRIVATE, ROLE ou ADMIN
- a string `service_role` em qualquer lugar do codigo

### A armadilha
A chave anon **nao autoriza nada sozinha** — ela so identifica o projeto. Quem autoriza e o RLS.
Publicar a anon key sem RLS ativo e o mesmo que publicar o banco inteiro.
Ver [[04-ativar-rls]].

### No [[ICook]]
Usa PostgreSQL no Supabase. Como o acesso passa pela API em ASP.NET Core (e nao direto do
Flutter), o risco esta contido — mas se algum dia o app falar direto com o Supabase, esta regra
vira critica.

## Relacionado
- [[04-ativar-rls]]
- [[01-esconder-api-keys]]
- [[Checklist-Seguranca]]
