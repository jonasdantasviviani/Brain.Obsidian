---
tipo: sessao
titulo: "Relatórios de estoque na base única (API + Web)"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, efcore, next, react, vitest]
tags: [tipo/sessao, relatorios, estoque]
palavras-chave: [kardex, curva ABC, estoque valorizado, produtos sem giro, abaixo do mínimo, EmpresasXEstoques, Movimentacoes]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# Relatórios de estoque na base única

PRs: API B-Tech-Sistemas/BTech.NFe.Api#70, Web B-Tech-Sistemas/BTech.Web#50 (branch `feat/relatorios-estoque`, base `feat/relatorios-base` porque #68/#48 não estavam mergeados). Base: [[2026-09-30-btech-base-relatorios]].

## O que ficou
- 9 relatórios em `/api/relatorios/estoque/<x>/paginado|exportar`: movimentacao e inventario (migrados; endpoints antigos removidos), kardex, posicao, posicao-grupo, valorizado, curva-abc, abaixo-minimo, sem-giro.
- Repositório em dois arquivos (`RelatorioEstoqueRepository.cs` e `.Produtos.cs`, partial). Relatórios por produto partem de `Produtos` com o saldo somado de `EmpresasXEstoques` por subconsulta (produto sem linha = 0).
- Web: 9 telas + `SeletorProduto`, `useGruposProdutoOpcoes`, `formatoEstoque.ts`; índice e menu atualizados.

## Premissas do schema (guardadas no CLAUDE.md da API)
- Saldo = `EmpresasXEstoques.Estoque` por (empresa, produto); movimento = `Movimentacoes` só-acrescenta com `QtdeMov` COM sinal e `QtdeAnterior/QtdeAtual` (snapshot do saldo da empresa). `TipoMov` E/S; sem tipo vale o sinal.
- Custo = `CustoProduto`, venda = `PrecoVenda`, `EstMin/EstMax` por produto (não por empresa).

## Armadilhas
- Curva ABC precisa do ranking inteiro (acumulado): não dá para paginar no banco; agrega no banco e classifica em memória (teto 50 mil produtos).
- `Contains` em InMemory é sensível a maiúsculas; no SQL Server não. Teste com o caso exato.
- Composite key sem coluna única (inventário empresa x produto): desempate por `IdProduto * 1e9 + IdEmpresa` na allowlist.
- Extrator do teste de contrato do Web só lê `request<T>(` com rota literal: não abstrair as chamadas de api.ts em helper com `${rota}`.
