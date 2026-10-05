---
tipo: sessao
titulo: Counter-Ragdoll - modo bomba nos mapas de_
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/counter-ragdoll, ia]
palavras-chave: [modo bomba, c4, desarme, kit, sitio, plantar, pavio, bip, portador, objetivo, bot preso, rota, perseguicao]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Counter-Ragdoll - modo bomba

## Resumo
Continuacao de "continue o que falta": depois de sniper e granadas, o item 2 do ROADMAP. So nos mapas
`de_` (os `cs_` sao resgate e o `fy_` e mata-mata, como no 1.6).

## O que foi feito
- `A`/`B` no desenho do mapa viram `Mapa.sitios`; marcados no de_poeira, de_inferninho e de_gelo.
- `Bomba` (dominio): comBot, caida, plantada, desarmada, explodiu; 3 s para plantar, pavio 40 s com bip
  que acelera, desarme 10 s (5 com kit), soltar zera.
- `Partida`: o primeiro bot carrega; sitio sorteado; portador planta parado no sitio sem ninguem a
  vista; morto, a bomba cai e o vivo mais perto busca; os outros guardam cantos do sitio
  (`Bot.objetivo`); explosao sem parede (raio 6, 500 no centro) e `manchaDaBomba`.
- `Confronto`/`Rodada`: plantada, o relogio nao encerra e eliminacao nao vence; explodiu -> Vermelhos,
  desarmada -> Azuis. Kit atravessa a rodada.
- Jogo: E segurado perto da bomba desarma e prende o jogador; longe, E continua sendo luneta. HUD sem
  relogio do pavio (como no 1.6), aviso "segure E", barra de desarme, borrao de tinta escorrendo.
- Loja: kit B 8 6 ($200). Sons: bip e "bomba plantada".

## Bugs achados medindo
Um teste de integracao (bomba plantada em ate 90 s no de_poeira) falhou e um rastreio por bot mostrou:
1. Bot chegando na **mesma celula** do lugar lembrado, a mais de 0,35 do ponto, ficava preso para
   sempre: a rota ate a propria celula vem vazia e a regra de chegada nunca roda. Existia antes da
   bomba. Correcao: rota vazia na mesma celula vira `[destino]`.
2. Perseguicao em linha reta: bot vendo o jogador no fim do corredor ficou 25 s empurrando a parede.
   Agora perseguir usa a rota da grade.
3. Portador saia para cacar e nunca plantava: `_destinoDesejado` poe o posto na frente da lembranca
   para quem carrega a bomba.
Ver [[ia-de-bot-que-procura-o-jogador]].

## Verificacao
321 testes (bomba, regras da rodada, kit, e a integracao no de_poeira real). Cenas em PNG da bomba
plantada, do aviso de desarme, da barra e da mancha. No navegador (build web `f03292d`), a rodada no
de_poeira abriu com "nao deixe plantarem a bomba"; plantar e desarmar nao foram jogados la - o
painel nao segura tecla presa. Depois de um build novo o painel recarregou a pagina sozinho entre um
screenshot e o clique: tire outro screenshot antes de clicar.

## Relacionado
- [[RagdollGames]]
- [[2026-09-12-counter-ragdoll-sniper-e-granadas]]
