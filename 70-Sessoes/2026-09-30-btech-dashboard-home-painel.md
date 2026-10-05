---
tipo: sessao
titulo: Painel de acompanhamento na home (dashboard) com gráficos
projeto: BTech
stack: [dotnet, nextjs, recharts, sqlserver]
tags: [dashboard, graficos, agregacao, tenant]
palavras-chave: [api/dashboard/painel, DashboardPainelRepository, PainelHome, recharts, useCarregar, GroupBy]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Painel da home
PRs: API #69 (mergear primeiro), Web #49 (branch feat/dashboard-home nos dois; worktrees ~/Code/wt-api-dashboard e ~/Code/wt-web-dashboard).
- `GET /api/dashboard/painel?de&ate&idEmpresa` no DashboardController EXISTENTE (já tinha /kpis); service/repo próprios (`DashboardPainelService`, `DashboardPainelRepository`) para não mexer no ctor do `DashboardService` (teste Unit o instancia). Só GROUP BY/SUM/COUNT no banco.
- As queries dos `Relatorio*Repository` materializam linhas (ToListAsync e soma em memória) — por isso o painel tem queries próprias em vez de reaproveitar.
- InMemory aceita qualquer LINQ: provei a tradução para SQL com teste em `Tests.SqlServer` (Testcontainers/Docker) — `GroupBy(_ => 1)`, subconsulta de estoque e join CorNota x CabNota traduzem OK.
- Armadilhas: InMemory usa só `Sequencia` como chave (tenants diferentes com mesma Sequencia colidem: usar faixas distintas no teste); `CorNotas.Sequencia` é IDENTITY no SQL Server (não setar); IDE0007 (usar var) quebra o build de teste.
- Web: recharts já era dependência; cores por `var(--chart-N)`; select nativo para filtros; `KpiCard` removido (sem uso). Contrato: `API_REPO=../wt-api-dashboard node scripts/atualizar-contrato.mjs`. Vitest agora inclui `tests/unit`.
- Não conferido no navegador contra API real.
Ver [[BTech.NFe.Api]] e [[BTech.Web]].
