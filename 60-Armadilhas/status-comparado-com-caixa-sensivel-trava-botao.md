---
tipo: armadilha
titulo: "Status comparado com == e caixa sensível deixou o botão de excluir morto para sempre"
projeto: [BTech.Web, BTech.NFe.Api]
stack: [next, react, typescript, dotnet, sqlserver]
tags: [tipo/armadilha, dados-legados, ui, importacao]
palavras-chave: [status, caixa alta, case sensitive, toUpperCase, botão desabilitado, ABERTO, Aberto, legado, importação, badge, cor de status]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Status comparado com == e caixa sensível deixou o botão de excluir morto para sempre

## Resumo
A tela de pedidos desabilitava o botão de excluir com `row.status !== 'ABERTO'`. O legado grava
`"Aberto"`, `"Pendente"`, `"Faturado"`, `"A"`, `"F"`. Todo pedido importado falhava a comparação e
o botão ficava **permanentemente desabilitado** — parecendo quebrado, não bloqueado.

O mais traiçoeiro: o `StatusBadge` da mesma tela fazia `status?.toUpperCase()`, então o rótulo
aparecia certo. A tela dizia "Aberto" e não deixava excluir. E o backend, com
`StringComparison.OrdinalIgnoreCase`, teria aceitado.

## O irmão do mesmo bug: todos os status com a mesma cor
O `StatusBadge` só conhecia `FECHADO`/`CANCELADO`/`ABERTO`. Tudo que o legado grava fora dessa
lista caía no mesmo `variant="secondary"` — "faturado e pendente têm a mesma cor" era literal.

## Correção
Vocabulário único nos dois lados: `Domain/Models/StatusPedido.cs` (backend) e
`lib/statusVisual.ts` (frontend), ambos reconhecendo as grafias do legado. O filtro da listagem
compara contra **todas** as grafias do status escolhido — `FECHADO` acha `F` e `Fechada`.

Para o que ninguém previu, a cor é derivada do próprio texto (hash → paleta): dois status
diferentes nunca terminam iguais, mesmo sem alguém ter mapeado. E o rótulo mostra o valor cru em
vez de virar "Rascunho" — some menos, e ajuda a descobrir o que o legado grava de verdade.

## Lição
Em sistema com base importada, **toda** comparação de string com dado do legado é suspeita.
Não é só normalizar na hora de exibir: se o rótulo normaliza e a regra não, a tela mente.

Relacionado: [[base-legada-btech-delphi]], [[entidade-sem-colecao-descarta-itens-no-binder]].
