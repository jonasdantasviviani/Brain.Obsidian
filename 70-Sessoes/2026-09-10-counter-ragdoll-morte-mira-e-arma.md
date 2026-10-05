---
tipo: sessao
titulo: Counter-Ragdoll - ragdoll na morte, mouse na mira e arma em primeira pessoa
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/fps]
palavras-chave: [ragdoll, morte, mouse, mira, pointer lock, cache, http.server, viewmodel, perspectiva, maos, arma]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: alta
---

# Counter-Ragdoll - morte, mira e arma
## Resumo
Tres entregas no `counter-ragdoll`: ragdoll de verdade na morte do bot, o mouse girando a
camera sem botao, e a arma redesenhada em primeira pessoa com maos.

## Contexto
Continuacao da [[2026-09-09-ragdollgames-serie-rabisco]]. Jogo em
`~/Documents/Repos/Games/counter-ragdoll`, servido em `localhost:5125` (servidor sem cache).

## Detalhe

### 1. Ragdoll na morte
Raycaster nao tem mundo fisico, entao a morte acontece num **palco a parte**: um so mundo
Forge2D com uma raia por cadaver, projetado no billboard do bot. Depois de 5 s o corpo vira
decalque e sai da fisica. Padrao em [[raycasting-em-flutter]].

### 2. Mouse na mira - a sessao que o cache comeu
Jonas relatou que o mouse so girava a camera clicando (e ai atirava). Foram seis rodadas de
correcao que **nao estavam rodando**: o `python -m http.server` nao manda `Cache-Control` e o
navegador servia o `main.dart.js` antigo. Ao trocar para um servidor com `no-store` na 5125,
funcionou de primeira. Ver [[http-server-do-python-serve-build-velho]].

Havia tambem um bug real por baixo: o `PonteiroTravado` era campo `late final` preguicoso, e
quando o getter que o lia saiu do HUD ele deixou de nascer - o listener de `mousemove` nunca
era registrado. Agora nasce no `onLoad`. E so um caminho de mira fica ligado por vez, senao o
mesmo movimento girava em dobro.

### 3. Arma em primeira pessoa
Jonas: "as armas estao de lado e nao apontando de frente". Redesenho completo como **trilho em
perspectiva** com ponto de fuga na mira, maos fechadas no punho e antebracos subindo dos
cantos. Tres ajustes que so apareceram na tela: traseira a ~0,80 da altura (mais baixo, as
maos somem), punho desenhado entre arma e mao, cotovelo medido a partir do pulso.

### Verificado e nao verificado
- Na tela: pistola e faca, com maos e antebracos.
- **Nao verificado na tela**: fuzil, escopeta e submetralhadora - so aparecem depois de pegar
  do chao, e o navegador do painel nao consegue andar ([[verificar-jogo-no-navegador-do-painel]]).
  Usam as mesmas primitivas da pistola.

### Resultado
flutter analyze limpo, 69 testes. Commits `a3d04db` (ragdoll), `3d7cc7e` (mouse),
e o da arma em primeira pessoa.

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[http-server-do-python-serve-build-velho]]
- [[verificar-jogo-no-navegador-do-painel]]
