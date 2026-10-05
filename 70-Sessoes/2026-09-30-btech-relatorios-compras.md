---
tipo: sessao
titulo: Relatórios de compras na base única
projeto: BTech
stack: [dotnet, nextjs]
tags: [relatorios, compras, entradas]
palavras-chave: [CabEntrada, CorEntrada, Debito.Identrada, custo medio, variacao de preco, RelatorioQuery]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Relatórios de compras
PRs: API #72, Web #52 (branch feat/relatorios-compras; base #68/#48 já estava na main).
- 6 relatórios em /api/relatorios/compras: entradas, compras-contas-pagar, ranking-fornecedores, custo-produto, variacao-preco, variacao-preco-mensal.
- CabEntrada.Status é texto livre (sem vocabulário conhecido); liga a Debito por Identrada; CorEntrada.Identrada = NumEntrada.
- Armadilha InMemory: left join com `t == null ? 0 : t.Total` e filtros sobre o DTO projetado estouram "Nullable object must have a value" (e nos totais). Use `(double?)t.Total ?? 0` e filtre ANTES de projetar. Não testado em SQL Server real.
- Web: apiRelatoriosCompras no fim de api.ts (fora do objeto api) e seletores próprios para não colidir com estoque.
Ver [[BTech]], [[Padrao-Relatorios-Base-Unica]].
