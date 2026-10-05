---
tipo: padrao
titulo: "Paginação no servidor: filtro no banco, e um endpoint só para os totais"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, efcore, next, react]
tags: [tipo/padrao, performance, api, ui]
palavras-chave: [paginação, filtro no servidor, fetchAllPages, debounce, totais, resumo, ordenação estável, DataTable, modo servidor]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Paginação no servidor: filtro no banco, e um endpoint só para os totais

## O problema que ele resolve
Tela que baixa a lista inteira (`fetchAllPages`) e filtra no navegador funciona até a base crescer.
Depois de importar o legado, abrir a tela de pedidos passava a baixar dezenas de milhares de linhas.

## As três peças
1. **Filtro no repositório**, não no serviço. Um objeto `Filtro*` com os campos da tela vira
   `IQueryable` encadeado. Nada de `FindAsync(...).Where(...)` em memória.
2. **Ordenação com desempate estável.** `OrderByDescending(DataEmissao)` sozinho faz linhas de
   mesma data trocarem de página entre duas requisições — e sumirem da listagem. Sempre um
   `ThenBy` por chave única.
3. **Endpoint separado para agregados.** Rodapé e cartões precisam do conjunto **filtrado inteiro**,
   não da página carregada: `/totais` (quantidade e soma) e `/resumo` (contagem por situação, numa
   consulta agrupada só). Somar as 20 linhas visíveis dá um número errado com cara de certo.

## Do lado da tela
- `useDebounce` no campo de busca: sem ele cada tecla vira requisição, e as respostas chegam fora
  de ordem.
- Mudar filtro **volta para a página 1** — seguir na página 7 de um resultado que agora tem 2
  mostra lista vazia.
- O `DataTable` ganhou modo servidor: com `onSearchChange`/`onSortChange` ele para de filtrar e
  ordenar `data` por conta própria. Refiltrar a página aberta esconderia o que está nas outras.
- Para lista que a API devolve inteira por ser pequena por natureza (usuários, tenants, resultado
  de relatório), `paginateLocally={20}` resolve sem endpoint novo.

Relacionado: [[BTech.Web]], [[base-legada-btech-delphi]].
