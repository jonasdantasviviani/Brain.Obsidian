---
tipo: decisao
titulo: Capacete segura o tiro na cabeca e se parte (dano x2)
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/decisao, dominio/jogos, jogo/counter-ragdoll, gameplay]
palavras-chave: [capacete, headshot, tiro na cabeca, colete, protecao, dano, cs 1.6, balanceamento]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Capacete segura o tiro na cabeca e se parte

## Resumo
No Counter-Ragdoll, tiro na cabeca **sem capacete mata na hora** (regra do Jonas). **Com capacete**,
o dano da arma passa dobrado e o capacete se parte - o segundo tiro na cabeca ja mata.

## Contexto
Em 2026-09-11 o capacete tinha ficado como "decisao pendente": com headshot letal ele nao teria
efeito. Em 2026-09-12 o Jonas decidiu: "capacete deve segurar HS sim, mas tomar dano".

## Detalhe
- x2 escolhido porque fuzil (34) derruba em dois e pistola (22) em tres: segura, mas quem leva sente.
- Partir no primeiro evita capacete eterno e deixa a leitura simples: "tinha capacete, agora nao tem".
- A mesma regra vale para bot e jogador (`Protecao.aparar`). Bots tambem acertam a cabeca
  (2% a 20% dos acertos), senao o capacete do jogador seria compra inutil.
- Colete nao protege a cabeca.
- No normal os bots usam so capacete; colete dobraria a vida efetiva deles.

## Alternativas descartadas
- Multiplicador x4 do 1.6 com capacete reduzindo: quebraria a regra "HS mata" que o Jonas quer.
- Capacete com pontos, como o colete: mais uma barra no HUD para pouco ganho de leitura.

## Relacionado
- [[2026-09-12-counter-ragdoll-capacete-placar-cenarios-lobby]]
- [[RagdollGames]]
