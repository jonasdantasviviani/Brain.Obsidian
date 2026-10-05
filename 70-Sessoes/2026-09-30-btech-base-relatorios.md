---
tipo: sessao
titulo: "Base única de relatórios (API + Web) com contas a receber migrado"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, efcore, next, react, vitest]
tags: [tipo/sessao, relatorios, paginacao]
palavras-chave: [RelatorioFiltro, RelatorioPaginado, RelatorioQuery, RelatorioLayout, useRelatorio, allowlist ordenação, exportação]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# Base única de relatórios

PRs: API B-Tech-Sistemas/BTech.NFe.Api#68, Web B-Tech-Sistemas/BTech.Web#48 (branches `feat/relatorios-base`).

## O que ficou
- API: `RelatorioFiltro`/`RelatorioPaginado<T>` (Domain/Models/Relatorios), `RelatorioQuery` + `RelatorioOrdenacoes<T>`
  (Infrastructure.Sql/Relatorios): paginação, allowlist tipada de ordenação com desempate, totais do filtro inteiro,
  exportação com teto de 50 mil (excede = `RelatorioExportacaoExcedidaException` -> 400).
- Contas a receber: `GET .../financeiro/contas-receber/paginado` e `/exportar` (endpoint antigo mantido).
- Web: `RelatorioLayout`, `useRelatorio` (filtros na URL + debounce), `relatorioFiltros.ts` (lógica pura testável).
- Padrão dos 5 passos documentado no CLAUDE.md dos dois repos. Ver [[paginacao-no-servidor-com-totais-separados]].

## Armadilhas
- Tests.Unit precisou do pacote `Microsoft.EntityFrameworkCore.InMemory` para testar o helper com EF de verdade.
- Tipo genérico (`RelatorioPaginado<T>`) vira schema OpenAPI com nome gigante (`CustomSchemaIds` usa FullName), então o teste
  de campos do contrato não o liga ao TS; a linha (`LinhaContaReceberDto`) liga.
- `useSearchParams` exige `<Suspense>` na página para o `next build`.
- Tests.Integration (SQL Server) falha sem banco local: falha preexistente, não relacionada.
