---
tipo: decisao
titulo: Módulo contratado vale para todo o tenant, aplicado no servidor; módulos só pelo admin do sistema
projeto: BTech.NFe.Api
stack: [dotnet, aspnetcore, multi-tenant]
tags: [modulos, permissoes, perfil, tenant]
palavras-chave: [AcessoMiddleware, ModuloExigido, GerenciarModulos, GerenciarPerfis, tenant padrão, X-Tenant-Id]
origem: pedido do Jonas 2026-10-06
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---
# Regra do Jonas
- Admin principal (tenant geral, `admin_sistema`) cadastra/edita/exclui tenants, liga módulos, restaura backup (importador) num tenant e cria usuários nele. O tenant padrão (com o login do admin) NÃO exclui nem desativa.
- Usuário só vê o tenant dele; o perfil escolhe as telas (finanças não emite nota). Módulo que o tenant não contratou some para TODOS, independente do usuário/perfil.
- Só o admin do sistema liga módulos; o admin do tenant cria usuários e perfis dentro dele.

# Como ficou
`CatalogoPermissoes.ModuloExigido` + `AcessoMiddleware` (403, cache invalidado), catálogo de perfil filtrado pelos módulos, policies `GerenciarModulos` (admin_sistema) e `GerenciarPerfis`. Tenant novo nasce sem módulos. Ver [[BTech.NFe.Api]] e [[entidade-bool-ativo-nasce-false-vira-tenant-inativo]]. PRs API #100, Web #76.
