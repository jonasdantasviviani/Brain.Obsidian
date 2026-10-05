---
tipo: padrao
titulo: Ragdoll em Flutter com Forge2D - esqueleto, juntas e consciencia
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [ragdoll, forge2d, box2d, flame, joint, revolute, limite de junta, ragdoll ativo, passo fixo, escala, desempenho]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: media
---

# Ragdoll em Flutter com Forge2D
## Resumo
11 corpos, 10 juntas com limite, e um multiplicador de "consciencia" no motor das juntas - e isso
que separa ragdoll de macarrao.

## Contexto
Padrao do [[RagdollGames]], **implementado e rodando** em
`~/Documents/Repos/Games/need-for-ragdoll`.

Atencao a versao: `forge2d 0.15` e Box2D **v3 nativa**, e a API antiga nao existe mais -
ver [[forge2d-e-box2d-v3]]. Detalhe em `~/Documents/Repos/Games/ragdoll-games/comum/motor-ragdoll.md`.

## Detalhe

### Esqueleto
Cabeca · torso-alto · torso-baixo · 2 bracos · 2 antebracos · 2 coxas · 2 canelas.
Juntas `revolute`, todas **com limite**:
pescoco -40..40 · ombro -170..170 · cotovelo 0..150 · quadril -90..45 · joelho -150..0 ·
coluna -30..30.

**Limite de junta e o item mais importante.** Sem limite o boneco se enrosca em si mesmo, e o
efeito para de ser engracado e vira bug visivel.

### Ragdoll ativo (o truque)
Na Box2D v3 isso sai da **mola da junta**, nao do motor: `enableSpring` + `targetAngle` +
`hertz`, com `springHertz = rigidezBase * consciencia`.

Boneco 100% mole nao joga - so cai. Motor de junta com forca variavel:
`forca = forcaBase * consciencia`.
- Em pe: motor forte tentando a pose alvo - cambaleia, mas anda.
- Tomou dano / bateu: `consciencia` despenca, o motor solta, vira trapo.
- Levantando: `consciencia` sobe de volta e o boneco se recompoe desajeitado.

Uma variavel resolve morte, capotamento, tropeco e nocaute nos jogos todos.

### Regras que nao podem ser quebradas
- **Passo fixo** de 1/60 s com acumulador e teto de 3 passos por frame. Passo variavel faz junta
  explodir e corpo atravessar parede - pior que em idle ([[motor-idle-em-flutter]]).
- **Escala em metros.** Box2D e metrico: boneco de 1,8 m, nao de 180 px. Modelar em pixel faz a
  gravidade parecer lua.
- **Desligar colisao entre partes do proprio boneco** (grupo negativo), senao a coxa empurra o
  torso e o boneco se chuta sozinho.
- **A fisica e invisivel**: o corpo pode ser uma capsula tosca, o desenho por cima e o que se ve.

### Desempenho
~8 ragdolls ativos e ~200 corpos no mundo, com 6/2 iteracoes. Corpo parado 2 s dorme
(`setAwake(false)`). Corpo morto sai da fisica em 5 s e vira decalque desenhado - identidade e
otimizacao na mesma decisao.

## Relacionado
- [[RagdollGames]]
- [[motor-idle-em-flutter]]
- [[flutter-puro-sem-engine-nos-jogos]]
