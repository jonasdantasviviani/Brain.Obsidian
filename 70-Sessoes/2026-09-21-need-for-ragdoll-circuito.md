---
tipo: sessao
titulo: Need for Ragdoll - pacote circuito (recreio, paredes giradas, PistaComponent)
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, jogo/ragdoll]
palavras-chave: [pista, recreio, eixo, mureta, catmull-rom, culling, polilinha]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: media
---

# Circuito do Need for Ragdoll
Continuacao de agente morto por limite de uso: Mureta.angulo, Pista.doEixo, marcos e grade ja
estavam commitados; faltavam Pista.recreio, fisica girada e o desenho.

- Recreio: 770 m, reta de 185 m, cotovelo de raio 10 m, 27 pontos de controle.
- Armadilha: **Catmull-Rom com 3 pontos num grampo de raio 12 m da raio 4 m**; precisa de 4 pontos
  no semicirculo. Prototipe a geometria em Python (mesma formula) antes de escrever o Dart.
- PistaComponent: duas polilinhas, pedacos de 30 m, culling por `CameraComponent.currentCameras`.
- Doc: `docs/decisoes/circuito.md` no repo.
