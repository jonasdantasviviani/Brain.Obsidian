---
tipo: decisao
titulo: Need for Ragdoll e visto de cima, com gravidade zero
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/decisao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [vista de cima, top-down, gravidade zero, camera, 2.5d, lateral, corrida, ragdoll, atrito]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-23
confianca: baixa
---

# Need for Ragdoll e visto de cima

> **REVOGADA PELO DONO em 2026-09-23.** Jonas, depois de ver o jogo rodando: "Ignore algumas
> decisoes do cerebro pensando no valor. Quero que seja o mais fiel ao NFS Underground. Apenas
> em rabiscos mas tendo todas as mecanicas e angulos de camera. O mundo aberto para chegar nas
> fases, a garagem, as atividades. Tudo."
> O criterio mudou: esta nota escolheu de cima **por custo**, e o dono decidiu que o valor esta
> na fidelidade. Vale como historico e como inventario do que a mudanca custa (o que depende de
> `gravity = Vector2.zero()`, do `arrastarNoChao`, do angulo inicial do boneco e da espessura em
> metrosPorPixel). A direcao nova esta em [[cara-de-need-for-speed-underground]].

## Resumo
A corrida acontece numa folha vista de cima, com `gravity = Vector2.zero()`; o que segura
carro e boneco na folha e o atrito, nao o peso.

## Contexto
O roteiro deixou em aberto: "2D de cima ou lateral 2.5D - decidir no prototipo. De cima e mais
simples e mais legivel". Os dias 1 a 5 rodaram **de lado**, com gravidade, porque o laboratorio
do ragdoll precisava de queda para ajustar junta e consciencia.

## Detalhe
Decidido **de cima**, pelo motivo previsto. O que muda na pratica:

- `JogoComFisica(gravidade: Vector2.zero())`;
- `CorpoRagdoll.manterEmPe` deixa de ser chamado neste jogo - equilibrio contra a gravidade nao
  significa nada numa vista de cima. Continua no motor, para os jogos laterais da serie;
- o que freia o boneco arremessado passa a ser `arrastarNoChao` (linear/angular damping alto),
  porque sem peso nao ha atrito de contato que o segure;
- o boneco nasce girado um quarto de volta (`anguloInicial = anguloDoCarro + π/2`), para a
  cabeca dele apontar para o capo;
- espessura de traco em **metros fixos** para de funcionar: a camera fica 3x mais longe e a
  pauta do papel sumiu. Toda espessura passa a ser `pixels * metrosPorPixel`.

### Alternativa descartada
Lateral 2.5D: capotamento fica mais espetacular (o boneco voa **para cima**), mas exige
paralaxe, ordem de profundidade e uma pista que se le em corte. Custo alto para um jogo cuja
graca esta no boneco solto, nao no relevo.

## Relacionado
- [[fisica-de-carro-arcade-visto-de-cima]]
- [[jogo-flame-estende-base-de-fisica-na-infra]]
- [[ragdoll-em-flutter-com-forge2d]]
