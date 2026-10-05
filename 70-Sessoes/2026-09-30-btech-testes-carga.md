---
tipo: sessao
titulo: BTech testes de carga e desempenho (retomada)
projeto: BTech.NFe.Api
stack: [dotnet, xunit, k6]
tags: [teste, carga, concorrencia]
palavras-chave: [N+1, k6, concorrencia, NumeroPedido, faturar, TrancaPorChave]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Testes de carga (PR #85, branch test/carga)

- Retomado apos limite de uso: commitado o que estava sujo e concluido k6 (tests/carga/k6), carga.yml, ConcorrenciaTests, ExportacaoNoTetoTests.
- Bugs achados por concorrencia: criar pedido (MAX+1 sem retry -> 400/PK) e faturar o mesmo pedido em paralelo (8 NF-e). Corrigidos com retry+jitter e [[TrancaPorChave]] por pedido (vale por instancia; multi-instancia precisa de indice unico).
- Armadilha: InMemory resolve joins em memoria; exportar 50 mil linhas leva minutos (12 min para 2 rotas), por isso so em CARGA_PESADA=1.
- Armadilha: TreatWarningsAsErrors (IDE0005/CS9113) quebra o build de teste por using/parametro sobrando.
- k6 nao instalado e Docker quebrado: scripts so validados com node --check.
