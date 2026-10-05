---
tipo: sessao
titulo: Counter-Ragdoll - bot que usa a mureta como cobertura
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/sessao, dominio/jogos, jogo/fps]
palavras-chave: [bot, cobertura, mureta, agachar, hitbox, encolher, pipoco, patrulha, mapa grande]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - bot que usa a mureta
## Resumo
O bot agora se abaixa atras da mureta enquanto a arma nao esta pronta e levanta para atirar.
Commit `8b6a63a`, 198 testes.

## Detalhe
- **Esconder tem de encolher.** A parte que importa: agachado, o corpo do bot vai a 72% da
  altura **no tiro e no desenho** (`Bot.regiaoNaAltura(z, agachamento:)`). Sem isso ele
  sumiria atras da mureta e continuaria levando bala do peito - esconderijo so visual.
- **O ciclo:** cobertura a ate 1,8 celula na linha do jogador + arma nao pronta = abaixa e
  segura a posicao (nao manobra). Pronto para atirar = levanta; os 0,18 s de levantar sao a
  janela do jogador. Abaixado ele **nao atira** - mesma regra do jogador.
- **Nao travar:** o `esperaDoTiro` corre mesmo abaixado, entao o ciclo sempre volta. Guardado
  por um teste de rodada inteira (30 s no `de_poeira`): os bots usam a mureta **e** acertam.
- Ele levanta ao perder o jogador de vista ou ao partir para cima (perseguindo).

## Achado a resolver
Medindo os seis cenarios (30 s, jogador parado no nascimento): em `de_inferninho` e
`cs_escritorio` **nenhum bot encontra o jogador**. A patrulha ("anda reto, vira 90 graus na
parede") nao da conta de mapa grande - e a navegacao por grafo da fase 9 do ROADMAP.

## Relacionado
- [[2026-09-11-counter-ragdoll-muretas]]
- [[RagdollGames]]
- [[raycasting-em-flutter]]
