---
tipo: sessao
titulo: Need for Ragdoll - integracao das sensacoes (nitro, capotamento, camera lenta)
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [nitro, multitoque, zona do nitro, capotamento, reinicio, camera lenta, dois relogios, sensacoes]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: media
---

# Integracao das sensacoes

## O que foi feito
- `domain/sensacoes`: `SensacoesDoPiloto` (tanque + impacto + reinicio, com a ordem certa) e `NitroDoRival`.
- `domain/entrada`: `MapaDeDedos` (multitoque, geometria injetada) e `DetectorDeToqueDuplo`.
- `presentation/jogo`: `SensacoesEmCena` (cola com fisica e camera) e `ZonaDoNitroComponent`.
- Hub do jogo: guidao pelo `SensacoesEmCena`, `dt` desacelerado no `update`, piloto recriado no ponto de retorno.
- `TelaDaCorrida`: `Listener` com mapa de ponteiros no lugar do `GestureDetector`.
- `CorpoCarro.dirigir(..., nitro)`.

## Aprendizados
- O reinicio corre em tempo real e o nitro/impacto no passo fixo: dois relogios. Ver decisao
  `docs/decisoes/integracao-sensacoes.md` no repo.
- [[medidor-de-impacto-com-cooldown-recapota-depois-do-reinicio]]
- Dominio testavel sem plataforma: a geometria do toque entrou por construtor, porque o espelho de teste
  dos agentes so enxerga `domain/` e `application/`.

## Nao verificado
Nada rodou em Flutter/Box2D; limiares de delta-v nunca foram sentidos jogando.

Projeto: [[RagdollGames]]
