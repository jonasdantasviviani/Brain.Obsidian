---
tipo: sessao
titulo: Counter-Ragdoll - seis mapas no espirito do 1.6, com papel e tinta proprios
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/sessao, dominio/jogos, jogo/fps]
palavras-chave: [mapa, cenario, de_poeira, fy_dia_de_piscina, cs_escritorio, de_gelo, papel, tinta, identidade visual, flood fill, validacao de mapa]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - seis mapas no espirito do 1.6
## Resumo
Jonas: "os mapas estao todos iguais, quero os do counter, fy pollday, the dust". Troquei os
tres cenarios genericos por **seis** com planta propria e, principalmente, dei **papel e tinta
proprios a cada um**. Commit `3ea5139`, 181 testes.

## Detalhe
- **A planta nao bastava.** Renderizei os seis lado a lado e eles continuavam com a mesma
  cara: mesma folha, mesma caneta. A identidade veio de `PapelDoCenario` (pautado,
  quadriculado, milimetrado, liso), `MatizDoPapel` (branca, kraft, gelo) e `TintaDoCenario`
  (azul, nanquim, grafite) no proprio `Mapa`. Vermelho ficou fora: e cor de inimigo.
- **Nomes:** nomenclatura do 1.6 (`de_`, `cs_`, `fy_`) com alusao, nao copia -
  `de_poeira`, `fy_dia_de_piscina`, `cs_escritorio`, `de_gelo`, `de_inferninho`, `cs_galpao`.
  Segue [[nomes-por-alusao-em-parodia]] e a regra da serie (layout proprio).
- **Validar antes de codificar:** escrevi os mapas em Python com flood fill a partir do
  nascimento. Tres dos seis tinham sala lacrada - inclusive o **galpao antigo**, cuja caixa
  de balas estava numa sala sem porta desde que nasceu. Virou
  `test/domain/cenarios_test.dart`, que recusa borda aberta, nascimento na parede, bot ou
  item ilhado, chao isolado e **dois mapas com o mesmo papel e tinta**.
- **Planta gerada por construcao:** o escritorio (12 salas) saiu de um gerador de corredores
  + salas + portas em Python; desenhar 36x21 na mao ia falhar de novo.
- A descricao da fase saiu da lista solta do menu e passou a morar no `Mapa`.

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[foto-de-cena-em-teste-flutter]]
- [[2026-09-11-counter-ragdoll-fase-7-movimento-e-recarga]]
