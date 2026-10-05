---
tipo: armadilha
titulo: "Update de entidade lida sem rastreamento, com a mesma já rastreada na requisição, estoura no EF"
projeto: [BTech.NFe.Api]
stack: [dotnet, efcore]
tags: [tipo/armadilha, stack/dotnet, efcore, fiscal]
palavras-chave: [already being tracked, AsNoTracking, FindIgnoreFiltersAsync, Update, consultar status, emitir, focus nfe, change tracker]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# Update de entidade lida sem rastreamento, com a mesma já rastreada, estoura no EF

## Resumo
`CabNotaService.AtualizarStatusFocusPorRefAsync` achava a nota com `FindIgnoreFiltersAsync`
(`AsNoTracking`, precisa ignorar o tenant por causa do webhook) e chamava `_repository.Update(nota)`.
Emitir e consultar já tinham carregado a MESMA nota rastreada na requisição →
`InvalidOperationException: another instance with the same key value is already being tracked` → 400.

## Efeito
- Consultar status na SEFAZ **nunca** funcionou.
- Emitir dava erro **depois** de a Focus aceitar a nota — a tela dizia falha de algo que foi enviado.
- Unitários não pegam (repositório mockado); só teste com DbContext real.

## Correção
Depois da busca sem rastreamento, `GetByIdAsync(chave)` (`FindAsync` devolve a instância já
rastreada se existir) e atualizar essa; se não houver (webhook sem tenant), usar a não rastreada.
Teste `AtualizarStatusFocus_ComANotaJaCarregadaNaRequisicao_Grava` em `PkPorTenantEfTests`.

## Lição
Método de serviço que faz "busca AsNoTracking + Update" é bomba quando o chamador já carregou a
entidade. Ver [[BTech.NFe.Api]], [[query-filter-nao-protege-update-entre-tenants]].

## Relacionado
- [[BTech.NFe.Api]]
- [[query-filter-nao-protege-update-entre-tenants]]
- [[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]]
