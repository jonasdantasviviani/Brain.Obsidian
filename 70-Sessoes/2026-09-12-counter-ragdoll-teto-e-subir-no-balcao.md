---
tipo: sessao
titulo: Counter-Ragdoll - teto desenhado, pulo que nao sai da fase e subir em balcao
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/counter-ragdoll, raycasting]
palavras-chave: [teto, forro, laje, trelica, pulo, salto, subir, balcao, engradado, colisao com altura, chaoSob, cabeNaAltura, tampo, drawRawPoints]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Counter-Ragdoll - teto e subir no balcao

## Resumo
Jonas: "nao consigo subir em balcoes, e quando eu pulo eu vejo por cima da parede, e o mapa some.
Faca um teto para nao parecer que estou saindo fora da fase". Tres sintomas, uma raiz: altura.

## Causa
- O pulo levava os pes a 0,73 - olho em 1,23, acima do topo das paredes (1). Acima delas so havia
  folha em branco, entao o jogador via "o lado de fora" do cenario.
- Nao existia teto desenhado: olhar para cima tambem mostrava papel cru.
- A colisao do corpo era so no plano (`Mapa.cabe`): balcao era parede para quem esta no chao e no ar.
- Balcao (0,58) e engradado (0,66) estavam acima do olho - com o teto a 3,2 m, eram moveis de 1,9 m.

## O que mudou
- Pulo alcanca 0,46 e a **cabeca bate no teto** (`1 - alturaDoCorpo`): olho no maximo 0,93.
- `Mapa.cabeNaAltura` (corpo de `pes` a `pes + altura`, contra as faixas solidas e o teto) e
  `Mapa.chaoSob` (topo mais alto debaixo de algum canto do corpo). O jogador guarda `alturaDoChao`
  e cai ate ela. Balcao 0,32 e engradado 0,38 sobem-se pulando; mureta (0,48) nao.
- Teto por mapa (`TetoDoCenario`: forro, laje, trelica, madeira) com o mesmo DDA do chao,
  espelhado, e marcas em lote com `drawRawPoints`.
- Tampo de balcao/engradado/mureta visto de cima (`Raio.distanciaDeSaida`).
- Bot enxerga pela altura real do olho do jogador: em cima do engradado a mureta nao esconde.
- `de_gelo`: os pilares de engradado viraram concreto, senao a arena ficava sem cobertura alta.

## Verificacao
279 testes (novos: sobe no balcao e desce do outro lado, sobe no engradado, mureta nao, olho nunca
passa do topo pulando do chao nem de cima do engradado, bot ve quem esta em cima). Cenas em PNG do
escritorio, galpao, balcao visto do chao e de cima, e do alto do pulo. No navegador, a partida no
`cs_escritorio` abriu com o forro e a luminaria acima das paredes. O pulo e a subida **nao** foram
testados jogando: o painel nao segura tecla presa (W + espaco), entao ficaram so nos testes.
Commit `c50e6c9`.

## Relacionado
- [[raycasting-em-flutter]]
- [[2026-09-12-counter-ragdoll-capacete-placar-cenarios-lobby]]
- [[RagdollGames]]
