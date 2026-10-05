---
tipo: armadilha
titulo: Selects mostrando o valor bruto em vez do rotulo
projeto: [btech-nfe-web, BTech.Web]
stack: [next, react]
tags: [tipo/armadilha, stack/next]
palavras-chave: [select, dropdown, rotulo, label, valor, codigo, ui, formulario, lookup, referencia]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Selects mostrando o valor bruto em vez do rotulo
## Resumo
Selects exibindo o codigo cru vindo da API em vez do texto legivel — 49 ocorrencias em 19 arquivos num unico projeto.

## Contexto
Corrigido no [[btech-nfe-web]], commit
`77df6e1 Corrige selects mostrando valor bruto em vez do rotulo (49 ocorrencias, 19 arquivos)`.

## Detalhe

### Por que espalha tanto
Cada tela monta seu proprio select a partir do payload da API. Quando o backend devolve
`{ codigo, descricao }` e o componente renderiza `codigo`, o bug se repete em toda tela que usa
aquele tipo de lookup — e cresce junto com o sistema.

### O que procurar
Campos de referencia: CFOP, CST, natureza de operacao, condicao de pagamento, vendedor, grupo de
produto, tipo de pagamento, centro de custo — todos os dominios de `ReferenceData` da
[[BTech.NFe.Api]].

### Como evitar de novo
Um unico componente de select de dominio que recebe a lista e sabe qual campo e rotulo e qual e
valor. Se cada tela decide isso sozinha, a 20a tela erra.

### Correlato no mesmo projeto
`8a636ef` e `d0fb5c1` — reconciliar `api.ts` com os controllers reais. A causa raiz e a mesma
familia: o front assumindo a forma do payload em vez de derivar do contrato.
Ver [[openapi-gerado-do-codigo]], que e a solucao estrutural para isso.

## Relacionado
- [[btech-nfe-web]]
- [[openapi-gerado-do-codigo]]
- [[BTech.NFe.Api]]
