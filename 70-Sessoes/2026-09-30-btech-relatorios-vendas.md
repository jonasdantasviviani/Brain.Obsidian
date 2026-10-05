---
tipo: sessao
titulo: BTech relatorios de vendas na base unica
projeto: BTech
stack: [dotnet, nextjs]
tags: [relatorios, vendas]
palavras-chave: [relatorios, vendas, curva abc, produtos mais vendidos, CorPedido tenant]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
Retomada da frente VENDAS (API PR #71, Web PR #51, branch feat/relatorios-vendas, base origin/main).
- Web tinha trabalho nao commitado: commitado e empurrado primeiro (limite de uso derruba agente).
- Removidos da API os endpoints antigos faturamento-mensal, top-clientes, vendas/comissao, vendas/tabela-preco (sem consumidor).
- produtos-mais-vendidos reusa por-produto com ordenar=quantidade.
- CorPedido tem IdTenant (CorPedido.Tenant.cs); joins comparam IdTenant mesmo assim.
- Armadilha: node_modules parcial (sem .bin) exige npm ci; bin/obj da API pesam, apagar ao fim.
Ligacoes: [[BTech]]
