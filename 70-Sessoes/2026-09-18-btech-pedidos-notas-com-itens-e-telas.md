---
tipo: sessao
titulo: "BTech — pedido/nota passam a gravar os itens, paginação no servidor e revisão das telas"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, efcore, sqlserver, next, react, typescript]
tags: [tipo/sessao, api, ui, performance, dados-legados]
palavras-chave: [pedido sem itens, nota vazia, faturar pedido, paginação, status legado, certificado múltiplo, sidebar, FormSheet, financeiro]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# BTech — pedido/nota passam a gravar os itens, paginação no servidor e revisão das telas

## O pedido do Jonas
Catorze queixas de uma vez, do "nada acontece" ao "deixe o formulário menos colorido".

## A descoberta que explicou metade delas
Três queixas diferentes — emissão de nota que não faz nada, pedido "criado" só na notificação, e
impressão sem valores — tinham **a mesma causa**: `CabPedido`/`CabNota` não têm coleção de
navegação, e o binder do MVC descartava `itens`/`corNota` em silêncio. O POST devolvia 201 com o
cabeçalho salvo e **zero itens**. Registrado em
[[entidade-sem-colecao-descarta-itens-no-binder]].

Outras duas queixas ("excluir não funciona", "faturado e pendente têm a mesma cor") também eram
uma só: comparação de status sensível a caixa contra dado importado —
[[status-comparado-com-caixa-sensivel-trava-botao]].

E "não há nada no financeiro" eram duas causas empilhadas: a tela só mostrava contas a **pagar**, e
três colunas liam campos que a API nunca mandou —
[[campos-json-camelcase-divergentes-na-tela-financeiro]].

## O que entrou

**Backend**
- `Itens` em `CabPedido`/`CabNota`, gravados numa transação só, com totais calculados no servidor
  ([[documento-com-itens-no-mesmo-corpo]]).
- `POST /api/pedidos/{id}/faturar`: gera CabNota + CorNotas a partir do pedido e liga os dois lados
  (`CabPedidos.NumeroNota` ↔ `CabNotas.NumeroPedido`) — era o que faltava para a tela de notas
  "puxar" um pedido. Não transmite: conferir antes continua sendo um passo separado.
- Filtro/ordenação/paginação no banco para pedidos e notas, mais `/totais` e `/resumo`
  ([[paginacao-no-servidor-com-totais-separados]]).
- `StatusPedido`: vocabulário único, reconhecendo as grafias do legado.
- Certificados: vários por empresa, com apelido escolhido por quem cadastra e coluna `Ativo`
  valendo. A tabela sempre aceitou; era o serviço que grudava em um só, com nome gerado do relógio.

**Frontend**
- `lib/statusVisual.ts`: cor e rótulo num lugar só, com cor derivada do texto para status que
  ninguém previu — dois status diferentes nunca terminam iguais.
- Tela de pedidos: paginação no servidor, exclusão destravada, visualização em página própria
  (duplo clique), seleção múltipla para faturar em lote, atalho de faturar por linha.
- Formulários de pedido: quatro faixas de gradiente saturado viraram cabeçalhos neutros.
- Sidebar: acordeão quando expandida, flyout com ponte e folga quando recolhida
  ([[flyout-de-menu-fecha-ao-atravessar-o-vao]]).
- `FormSheet`: a prop `size` passou a funcionar ([[classe-do-primitivo-ui-vence-a-prop-size]]).
- Financeiro com as duas contas; certificados com lista, ativação e exclusão.

## Verificação
Não parei nos testes: subi a API contra o SQL Server real e passei o fluxo inteiro por curl —
POST com itens, itens conferidos direto no banco com o `IdTenant` certo, PUT preservando e
substituindo, faturar gerando nota com itens, guardas recusando, filtro `status=FECHADO` achando
o `'F'` do legado e `status=ABERTO` achando `'Aberto'` em caixa mista.

Duas coisas que só apareceram nessa verificação:
- `CorPedidos.Sequencia` é IDENTITY **da tabela**, não do pedido: um pedido chega com itens 3, 4 e
  5, e o `addItem` da tela criava o próximo como `length + 1` = 4, colidindo. Renumerado na carga.
- O valor adulterado no cliente (`valorTotal: 999999`) voltou como 200 — o recálculo no servidor
  segura.

## Ficou pendente
- `npx eslint` não termina nesta máquina (node_modules com ~40 mil arquivos despejados pelo iCloud
  — ver [[BTech.Web]]). `tsc --noEmit` roda, leva uns 5 min, e passou limpo.
- ~20 arquivos `"<nome> 2.tsx"` não rastreados no BTech.Web, cópias byte a byte de conflito do
  iCloud. São lixo, mas apagar arquivo do Jonas é decisão dele.
