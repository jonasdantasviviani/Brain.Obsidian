---
tipo: sessao
titulo: Produto e serviço num cadastro só, pedido com itens únicos (API #81 + Web #61)
projeto: BTech
stack: [dotnet, nextjs, sqlserver]
tags: [produto, servico, pedido, nfse, faturamento, migracao]
palavras-chave: [Produtos.Tipo, ProdutosServico, PedidosServicos, ServicosNfse, CorPedidos, migracao 035, faturar, grade unica, SeletorProduto, servicos-nfse obsoleto]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: media
---
# Produto e serviço num cadastro só

- Pedido do Jonas: "unificar cadastros": tela de produto com Tipo (produto/serviço), serviço e produto no mesmo pedido, faturar agrupando todos os produtos (NF-e) e todos os serviços (NFS-e). Decisão e alternativas em [[produto-e-servico-num-cadastro-so-com-tipo]]. Continua [[2026-09-30-btech-pedido-com-servicos-e-faturamento-misto]] e [[2026-09-30-btech-status-de-notas-na-lista-de-pedidos]].
- **API #81**: migração `035` (`Produtos.Tipo` P/S com CHECK, tabela 1:1 `ProdutosServico`, `CorPedidos.Desconto/Discriminacao/AliquotaIss`, marcas `ServicosNfse.CodProduto` e `PedidosServicos.MigradoEm/CorSequencia`) com a migração de dados na própria 035, em transação e reaplicável. `CabPedidoService` ficou com uma lista só (`itens`), tipo vindo do produto, totais por tipo; `PedidoFaturamentoService` separa por tipo; nota de produto recusa S (`ItensSoDeProduto`); relatórios de estoque só P; `/api/servicos-nfse` virou camada obsoleta sobre produtos S. `ServicoNfse`/`PedidoServico` saíram do código (tabelas ficam).
- **Web #61**: `lib/pedidoItens.ts` (lógica pura), `components/pedidos/ItensPedido.tsx` (grade única), `SeletorProduto` com `tipo='P'|'S'|'todos'`, cadastro de produto com Tipo (`lib/produtoTipo.ts`), rotas antigas redirecionam. Parte do Web (cadastro, menu, EditorNfse) foi feita por subagente em paralelo no mesmo worktree, commitando só os seus arquivos.
- **Risco não fechado**: a migração 035 só passou no parser T-SQL (ScriptDom) e ficou com teste em `Tests.SqlServer/MigracaoProdutoServicoTests` que NÃO rodou: o Docker da máquina estava com o store corrompido. Rodar antes do deploy. Ver [[docker-do-banco-nao-sobe-no-mac]].
- Armadilhas: teste funcional que fixava `idProduto = 1` passou a cair num serviço criado antes (InMemory reaproveita o código); criar o produto no teste. `Produtos.CodProduto` é IDENTITY: inserir em lote exige `SET IDENTITY_INSERT` e numeração por tenant (`MAX + ROW_NUMBER`). `MERGE ... ON 1 = 0 ... OUTPUT` é o jeito de ler a Sequencia IDENTITY e a chave de origem no mesmo insert.
- Não feito: coluna "tipo" nos relatórios de vendas, conferência visual no navegador, destaque do item "Serviços" no menu.
