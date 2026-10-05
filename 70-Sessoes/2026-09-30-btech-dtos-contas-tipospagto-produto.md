---
tipo: sessao
titulo: DTOs de ContasCors, TiposPagto e Produto (API #64 + Web #44)
projeto: BTech
stack: [dotnet, nextjs]
tags: [seguranca, dto, mass-assignment]
palavras-chave: [ContasCors, TiposPagto, Produto, ResumoResponse, CriarRequest, MascaraBancaria]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# DTOs de ContasCors, TiposPagto e Produto

- API PR #64 (empilhado no #63) e Web PR #44 (base main; #43 ja mergeado).
- Padrao: Resumo na lista, Response completo no GET/POST/PUT, CriarRequest no POST, PUT parcial com `protegidos` para campos de sistema.
- Armadilha: front de produtos lia `codigoComercial`/`codNcm` (inexistentes na API); o certo e `codComercial`/`ncm`.
- Worktrees em ~/Code/wt-api-dtos2 e ~/Code/wt-web-dtos2 (nunca em ~/Documents).
