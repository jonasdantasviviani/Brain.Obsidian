---
tipo: sessao
titulo: Fechar pedido move estoque e financeiro sempre; painel por módulo; importação que não sobrescreve
projeto: BTech
stack: [dotnet, nextjs, sqlserver]
tags: [pedido, estoque, financeiro, importador, dashboard]
palavras-chave: [faturar, comNota, previsto, contas a receber, Substituir, blocos do painel]
origem: pedido do Jonas 2026-10-07
criado: 2026-10-07
atualizado: 2026-10-07
confianca: alta
---
Prioridades do Jonas: cadastros, pedidos, emissão de nota produto/serviço, e ao fechar movimentar financeiro e estoque (100%).
- Decisão dele: ao faturar pergunta "previsto (com nota) / não previsto (sem nota)" e estoque+financeiro movem SEMPRE. PRs API #102, Web #78.
- Checklist do pedido verde com lista vazia (bug); painel só com blocos dos módulos/perfil (API #101, Web #77); importador agora só acrescenta por PK (Substituir explícito) — antes apagava o tenant a cada reimportação. Testado em SQL Server real via docker.
- Fila combinada (um PR cada): NCM→CFOP no cadastro, devolução pela nota de entrada, boletos/CNAB.
Armadilha: [[entidade-bool-ativo-nasce-false-vira-tenant-inativo]] (teto de consultas por requisição sobe quando se adiciona trabalho fixo; criar tranca por empresa para baixa concorrente de estoque).
