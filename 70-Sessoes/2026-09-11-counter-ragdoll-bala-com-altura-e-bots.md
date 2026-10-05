---
tipo: sessao
titulo: Counter-Ragdoll - bala com altura, headshot e bots mais realistas
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, dominio/jogos, jogo/fps]
palavras-chave: [headshot, dano por regiao, balistica, altura da bala, bot, animacao, arma na mao, manobra, loja, foco do teclado]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - bala com altura, headshot e bots mais realistas
## Resumo
Pedido do Jonas: bots sem arma na mao, so andando de lado, sem animacao de tiro; tiro na parede
sempre na mesma altura; perna e cabeca com o mesmo dano - "HS mata na hora". Tudo feito, commit
`0fb6031`, 135 testes.

## Contexto
Continuacao de [[2026-09-10-counter-ragdoll-combate-menu-e-som]]. Antes de comecar, conferi no
git e **corrigi uma afirmacao errada minha**: eu tinha dito que a regra "uma tentativa" da trava
do mouse nunca entrou no commit `9f1bb90` - ela entrou
([[commit-que-descreve-o-que-nao-foi-feito]]).

## Detalhe
- **Balistica:** `Jogador.alturaDoTiro(d)`; bala para no chao/teto; regioes do bot
  (`RegiaoDoCorpo`: cabeca letal, tronco x1, pernas x0,75); cabeca mais estreita.
  Padrao em [[raycasting-em-flutter]].
- **Achado no caminho:** o coice era aplicado **antes** de a bala sair - a primeira bala ja
  saia acima da mira e, com altura, virava headshot sozinha. Invertido.
- **HUD/som:** X maior + "na cabeça" + `som.naCabeca()`.
- **Bots:** maquina de manobras (parar/mover em 6 direcoes), precisao andando x0,55, passada
  animada, pose por direcao, coice e clarao na boca do cano.
- **Loja conferida no painel:** B 8 1 comprou colete ($800 -> $150). O teclado depois do menu
  estava morto por falta de foco - `FocusNode` + `requestFocus` no `GameWidget`. Categoria vazia
  (pistolas) agora avisa em vez de abrir lista vazia.
- **Decisao do Jonas:** headshot mata sempre (no 1.6 e x4). Registrado no ROADMAP.
- **Consequencia a observar:** com a bala subindo junto com o coice, a rajada de fuzil a 6
  celulas passa por cima da cabeca a partir do 2o tiro. E fiel a mira na tela; se ficar dificil
  demais, reduzir o coice por tiro (`levarRecuo`, fator 0,012).

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[foto-de-cena-em-teste-flutter]]
- [[verificar-jogo-no-navegador-do-painel]]
