---
tipo: decisao
titulo: Produto e serviço num cadastro só, com Tipo, em vez de dois cadastros
projeto: BTech
stack: [dotnet, sqlserver, nextjs]
tags: [tipo/decisao, dominio/fiscal, modelo]
palavras-chave: [Produtos.Tipo, ProdutosServico, tabela 1:1, ServicosNfse, PedidosServicos, CorPedidos, unificar cadastros, NF-e NFS-e, estoque]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: media
---
# Produto e serviço num cadastro só, com Tipo

## Resumo
`Produtos.Tipo` ('P' padrão | 'S') unifica a escolha em todas as telas; o dado fiscal do serviço mora numa tabela 1:1 `ProdutosServico` e o item do pedido (`CorPedidos`) aponta para produto de qualquer tipo. Faturar agrupa por tipo: todos os P numa NF-e, todos os S numa NFS-e.

## Contexto
Antes (API #79): dois cadastros (`Produtos` e `ServicosNfse`) e duas listas no pedido (`CorPedidos` e `PedidosServicos`), com duas seções/abas na tela. O Jonas pediu unificar: um seletor, um cadastro. Ver [[2026-09-30-btech-produto-e-servico-unificados]].

## Decisão e alternativas
- **Tipo em `Produtos`** (escolhida) em vez de manter os dois e só unir a tela: o seletor, o pedido, o faturamento e os relatórios passam a ter um critério só.
- **Dado fiscal em tabela 1:1**, não em colunas de `Produtos`: a tabela é larga, legada e copiada coluna a coluna pelo importador; mesmo motivo de `ProdutosCest` (ver [[pk-por-tenant-para-preservar-numero-do-legado]]). Custo: um join/consulta a mais, resolvido em lote na lista (`ObterServicosAsync`).
- **Desconto/discriminação/ISS da linha em `CorPedidos`** (3 colunas nulas) em vez de tabela lateral: só o serviço usa, e o pedido precisa editar por linha.
- **Migração dentro da 035**, idempotente por marca na linha (`MigradoEm`, `CodProduto`), sem apagar tabelas: dá para conferir e voltar atrás.
- Tipo vem sempre do produto, nunca do corpo (over-posting); trocar o tipo de produto já usado é recusado (400) porque bagunçaria estoque e notas emitidas.
- Serviço não tem NCM/CEST/estoque/peso: o servidor descarta, e nota de produto, estoque e relatórios de estoque só enxergam P.

## Consequências
- Descrição passa a caber em 100 caracteres (validador do produto); o texto inteiro vai para a discriminação padrão.
- O dado fiscal da linha migrada agora vem do cadastro (antes era cópia); NFS-e já gravada não muda.
- `/api/servicos-nfse` ficou obsoleta (camada fina); sai quando ninguém a usar. Relacionado: [[put-parcial-sobre-o-registro-existente]].
