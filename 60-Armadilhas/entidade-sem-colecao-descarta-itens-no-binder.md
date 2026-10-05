---
tipo: armadilha
titulo: "Entidade sem coleção de navegação: o binder do MVC descartava os itens em silêncio"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, aspnetcore, efcore, next]
tags: [tipo/armadilha, aspnetcore, model-binding, contrato-de-api, dados]
palavras-chave: [model binding, FromBody, coleção de navegação, NotMapped, itens, CorPedido, CorNota, pedido vazio, nota vazia, 201 sem gravar, scaffold]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Entidade sem coleção de navegação: o binder do MVC descartava os itens em silêncio

## Resumo
As telas de pedido e de nota sempre mandaram cabeçalho **e itens** no mesmo corpo. As entidades
escalfoldadas (`CabPedido`, `CabNota`) **não têm coleção de navegação**. `[FromBody] CabPedido`
ignora propriedade JSON desconhecida por padrão: `itens` era jogado fora, o POST respondia **201
com o cabeçalho salvo** e nenhum item.

## Sintoma
"O pedido é criado apenas visualmente na notificação, mas nada acontece."
"A emissão de notas não funciona, apenas aparece que enviou no front."

O toast de sucesso era honesto do ponto de vista do HTTP — o 201 existiu. O que não existiu foi o
pedido de verdade: linha em `CabPedidos` sem nenhum `CorPedidos`, valor nulo, e no fim da lista
(número = máximo + 1, depois da importação isso é ~50.000), onde ninguém olha.

Efeito dominó, tudo com a mesma causa:
- impressão saía com tabela de produtos vazia e total zerado;
- a nota gerada a partir do pedido saía sem item — e a Focus recusa nota sem item;
- o valor da listagem mostrava "—" porque o frontend também nunca mandou os totais.

## Por que passou despercebido
Nada falha. Não há exceção, log, nem status de erro. O contrato do frontend era **ficção**: ele
falava um formato que o backend nunca implementou, e os dois lados compilavam.

## Correção
Propriedade `[NotMapped] List<CorPedido>? Itens` num partial (`CabPedido.Itens.cs`), o serviço
gravando cabeçalho e itens **no mesmo `SaveChanges`** (uma transação só, sem pedido pela metade),
e os totais calculados no servidor a partir dos itens.

`AtualizarParcialAsync` só aplica escalares de propósito, então no PUT os itens são lidos pelo
controller e entregues no `antesDeSalvar`: ausente preserva, presente substitui a lista inteira.

## Lição
Quando o frontend manda um campo que "não faz nada", a primeira hipótese não é bug de tela — é
o campo **não existir** no tipo que o binder desserializa. Vale conferir o contrato dos dois lados
antes de procurar em qualquer outro lugar.

Relacionado: [[importador-casava-coluna-pelo-nome-da-propriedade]] (mesma família: dado descartado
em silêncio por nome que não casa), [[status-comparado-com-caixa-sensivel-trava-botao]].
