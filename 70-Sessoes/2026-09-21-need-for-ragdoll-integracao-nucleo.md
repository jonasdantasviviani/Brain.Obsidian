---
tipo: sessao
titulo: Need for Ragdoll - integracao do nucleo da corrida no jogo
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [piloto em cena, corrida, passo fixo, grade, resultado, pontos de extensao, hub]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: media
---

# Integracao do nucleo da corrida

O `NeedForRagdollGame` virou uma lista de `PilotoEmCena` ligada a `Corrida` no
passo fixo, com pista por parametro (recreio, ou patio como treino livre) e
tela de resultado sem derrota. Decisoes completas em `docs/decisoes/integracao-nucleo.md`
do repositorio.

- O que se pode decidir sem Flame/Box2D foi para o dominio (`MontagemDaCorrida`,
  `ResumoDoResultado`) para testar: o ambiente so roda `test/domain`.
- `CarroComponent` copia a pose so no `update`: antes de `camera.follow(snap: true)`
  copie a pose na mao, senao o primeiro quadro sai na origem do mundo.
- Trava de largada: `Guidao(freioDeMao: true)`, nao `acelerador: -1` (poe de re).
- Grupo de colisao negativo unico por piloto; o mesmo grupo nunca colide.
- Ligado: [[Need-for-Ragdoll-roadmap-em-paralelo]] (sessao irma, mesma data).
