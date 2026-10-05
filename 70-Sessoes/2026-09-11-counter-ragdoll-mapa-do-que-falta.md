---
tipo: sessao
titulo: Counter-Ragdoll - mapa do que falta (52% do ROADMAP pronto)
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/sessao, dominio/jogos, jogo/fps, tema/planejamento]
palavras-chave: [roadmap, planejamento, pendencias, dependencias, bomba, granada, bots aliados, som, divida tecnica]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - mapa do que falta
## Resumo
Jonas pediu "mapeie o que falta". Levantei o ROADMAP inteiro contra o codigo: **46 itens
prontos, 16 parciais, 26 a fazer** (52%). Commit `fb18890`.

## Detalhe
- **Conferir antes de listar:** tres itens estavam marcados como pendentes e ja existiam no
  codigo - HUD no lugar do 1.6, mensagens de rodada e decalque dos corpos - e o colete
  aparecia como `[ ]` na fase 7 enquanto a fase 5 dizia que ele segura 50%. Lista de
  pendencia que nao e conferida vira ficcao.
- **Ordem por impacto, nao por numero de fase:** (1) ouvir o som, (2) modo bomba, (3) bots
  aliados, (4) granadas, (5) lote de coisas pequenas, (6) fidelidade fina, (7) decisoes.
- **Dependencia explicita:** bomba precisa de zonas no mapa e de bot que planta; radar,
  placar no TAB e lista de abates so fazem sentido depois de bots aliados. Sem isso a fase 11
  parece pequena e nao e.
- **Capacete virou decisao, nao tarefa:** com "tiro na cabeca mata na hora" (escolha do
  Jonas), capacete nao tem efeito - implementar seria contradizer a regra.
- **Divida registrada no proprio ROADMAP:** os sons da fase 2 nunca foram ouvidos (dez itens
  `[~]` sem confirmacao) e, desde que o painel do navegador ficou oculto, a verificacao vem
  so de teste e de cena renderizada em PNG.

## Relacionado
- [[RagdollGames]]
- [[2026-09-11-counter-ragdoll-navegacao-dos-bots]]
- [[verificar-jogo-no-navegador-do-painel]]
