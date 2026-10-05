---
tipo: armadilha
titulo: "Submenu que só abre no hover fecha ao atravessar o vão até ele"
projeto: [BTech.Web]
stack: [next, react, css]
tags: [tipo/armadilha, ui, usabilidade]
palavras-chave: [flyout, hover, mouseleave, submenu, sidebar, menu colapsado, ponte, grace period, acordeão]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Submenu que só abre no hover fecha ao atravessar o vão até ele

## Resumo
A sidebar abria o submenu no `onMouseEnter` e fechava no `onMouseLeave`, sem atraso, com o painel
posicionado a `ml-1` (4px) do botão. Esses 4px estão fora da caixa do wrapper: o ponteiro sai,
`mouseleave` dispara, o menu fecha antes de chegar no destino. Pior no modo recolhido — que é o
padrão da tela e onde não há rótulo nenhum para orientar.

## As três correções
1. **Ponte**: o padding (`pl-2`) fica no wrapper posicionado, não como margem do painel. O vão
   passa a ser área do próprio elemento, e o ponteiro nunca sai.
2. **Folga para fechar**: `setTimeout` de 250ms no `mouseleave`, cancelado se o ponteiro voltar.
   Perdoa o movimento diagonal, que é como as pessoas de fato mexem o mouse.
3. **Clique fixa**: estado separado (`fixado` vs `sobrevoado`). Dá para clicar, soltar o mouse e
   ler o menu com calma. Fecha com Esc ou ao navegar.

No modo **expandido** o flyout foi trocado por acordeão em linha: sem hover, sem timing, sem
sobrepor o conteúdo — e o grupo da rota atual já abre sozinho.

## Lição
Menu por hover é um teste de precisão de mouse que ninguém pediu para fazer. Se o desenho tem um
vão entre gatilho e painel, ou a ponte cobre o vão ou o menu vai fechar na cara do usuário.

Relacionado: [[BTech.Web]].
