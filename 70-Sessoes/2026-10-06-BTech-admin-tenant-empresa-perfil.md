---
tipo: sessao
titulo: Admin do sistema - excluir/desativar tenant, empresas e perfis por tenant, perfil limita telas
projeto: BTech
stack: [dotnet, nextjs]
tags: [multi-tenant, permissoes, admin]
palavras-chave: [tenant, perfil, X-Tenant-Id, CatalogoPermissoes, PerfilForm]
origem: sessao claude code
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---
PRs: API #98, Web #74. Decisões do Jonas: empresas dentro do tenant (sem mover); excluir só se vazio, senão desativar; usuário em UM tenant, admin do sistema escolhe o tenant, perfil define telas. Detalhes técnicos em [[entidade-bool-ativo-nasce-false-vira-tenant-inativo]] e na seção "Tenant, empresa e perfil" do CLAUDE.md da API. Web não foi verificado no navegador (mock sem endpoints novos).
