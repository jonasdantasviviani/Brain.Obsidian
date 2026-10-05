---
tipo: armadilha
titulo: "Prop `size` do FormSheet não fazia nada: a classe embutida do primitivo empatava e vencia"
projeto: [BTech.Web]
stack: [next, react, tailwind, base-ui]
tags: [tipo/armadilha, tailwind, css, ui]
palavras-chave: [tailwind, especificidade, max-w, data-side, important, shadcn, base-ui, sheet, FormSheet, prop ignorada]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Prop `size` do FormSheet não fazia nada

## Resumo
`FormSheet` aceitava `size` (`sm`/`md`/`lg`/`xl`) e passava `sm:max-w-lg` na className. O
`SheetContent` já traz `data-[side=right]:sm:max-w-sm` embutido. As duas regras têm especificidade
equivalente — quem ganha depende da ordem no CSS gerado, não da ordem na string de classes. Na
prática o painel ficava sempre em 384px, com a prop parecendo funcionar.

Formulário de empresa tem 20 campos em `grid-cols-2`. Em 384px as colunas colapsam e vira uma tira
estreita e comprida: "o menu de edição que abre lateralmente está muito ruim".

## Correção
Modificador important do Tailwind v4 (sufixo `!`): `sm:max-w-3xl!`. Junto: cabeçalho fixo
(`shrink-0`) + corpo com `min-h-0 flex-1 overflow-y-auto` — sem `min-h-0` o filho estica o
container flex e a rolagem sobe para o painel inteiro, levando o cabeçalho junto.

## Lição
Passar className para um componente que já tem a mesma utilidade embutida não é override — é
empate. Ou o componente expõe um slot de verdade, ou usa `!`, ou o valor "configurável" é enfeite.

Relacionado: [[BTech.Web]].
