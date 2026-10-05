---
tipo: sessao
titulo: DTOs de Pedido, Nota fiscal e Entrada (API #65 #66 #67 + Web #45 #46 #47)
projeto: BTech
stack: [dotnet, nextjs]
tags: [seguranca, dto, mass-assignment, fiscal]
palavras-chave: [CabPedido, CabNota, CabEntrada, CorPedido, CorNota, CorEntrada, itens no mesmo corpo, TodosExceto, lista de permissao, NotaEditavel, StatusFocus, RefFocus, ChaveNfe, over-posting]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# DTOs de Pedido, Nota fiscal e Entrada

- Tres familias empilhadas (API: #65 pedidos < #66 notas < #67 entradas, base #63 que ja contem o #64 squash-mergeado; Web: #45 < #46 < #47, base origin/main). Worktrees em ~/Code/wt-api-{pedidos,notas} e wt-web-{pedidos,notas,entradas}.
- Padrao novo: PUT parcial por LISTA DE PERMISSAO (`AtualizacaoParcial.TodosExceto<T>(editaveis)` gera o `protegidos`): coluna nova nasce protegida. Ver [[put-parcial-sobre-o-registro-existente]] e [[documento-com-itens-no-mesmo-corpo]].
- Itens no mesmo corpo continuam: `PedidoItemRequest`/`NotaItemRequest` no `Itens` do request; no PUT `TentarLer` (null = preserva, lista = substitui, tipo errado = 400 no campo `itens`). PUT sem `itens` precisa reler o documento para a resposta trazer os itens (a entidade salva volta com `Itens = null`). Ver [[entidade-sem-colecao-descarta-itens-no-binder]].
- Status fiscal so o servidor altera: nota nasce Status "0" (rascunho; painel admin conta assim), TipoMov "S"; StatusFocus/RefFocus/ChaveNfe/Idnfe/Idprotocolo/NumeroNota/NumeroPedido nunca vem do corpo. Emissao/conferencia leem o banco, nao mudaram.
- Achados: (1) `CorNotaController` (/api/itens-nota) ignorava a trava de nota transmitida: extraida para `NotaEditavel`; (2) `CorPedido` nao e `ITenantScoped` (coluna IdTenant existe, entidade nao mapeia): itens avulsos agora checam o pedido pelo tenant, mas `GET /api/pedidos/itens` (lista global) ainda nao filtra por tenant; (3) pedido lia `idProduto` mas a API devolvia `idproduto`: editar pedido perdia o produto dos itens (DTO devolve `idProduto`); (4) `NotaFiscalForm` mandava `corNota`/`consumiFinal` (nomes que a API nunca leu).
- Armadilha de teste (front): em `tests/contract/campos.test.ts` o `{p}` casava com `/totais` e `/itens`; corrigido para casar so com parametro de rota.
- Sobrou: colunas Serie (`serieNfeSrv`) e `temNfeProc` da grade de notas nao existem na API; `Xml` da entrada fica fora das respostas; arredondamento de centavos no total da nota e do servidor (sem Math.Round por item).
