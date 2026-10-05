---
tipo: armadilha
titulo: Medidor de impacto com cooldown recapota o carro quando o reinicio acaba
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/armadilha, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [classificador de impacto, cooldown, pico, delta-v, reinicio, capotamento, teletransporte, zerar, avaliarVelocidade]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: alta
---

# Medidor de impacto com cooldown recapota o carro quando o reinicio acaba

## Resumo
O `ClassificadorDeImpacto` guarda o **pico** da batida e um cooldown de 0,4 s que so anda quando alguem
chama `avaliar`. Se o jogo para de medir durante o reinicio (2 s), o pico e o cooldown ficam congelados e,
no primeiro passo depois do reinicio, o medidor **le o pico velho e capota de novo**.

## Sintoma
Capotou, voltou a pista e capotou outra vez sozinho, em loop. Nos testes: uma batida de 20 m/s a zero,
2 s de reinicio, e o proximo `medir` devolvia `capotamento` com o carro parado.

## Correcao
Durante o reinicio chame `impacto.zerar()` (nao so `esquecerVelocidade()`) a cada passo, e de novo ao
recolocar o carro. Assim o teletransporte de 20 m/s para 0 tambem nao vira "batida".
Feito em `SensacoesDoPiloto.medir` / `aoReposicionar` (`lib/domain/sensacoes/`).

## Relacionado
[[RagdollGames]] · [[2026-09-21-need-for-ragdoll-integracao-sensacoes]]
