---
tipo: sessao
titulo: Counter-Ragdoll - navegacao dos bots pela grade, ronda e ouvido
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/sessao, dominio/jogos, jogo/fps, tema/ia]
palavras-chave: [pathfinding, busca em largura, bfs, grafo, patrulha, ronda, ouvir tiro, ultimo lugar visto, bot, navegacao]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - navegacao dos bots
## Resumo
Os bots passaram a achar o jogador em **todos** os seis cenarios (3 a 28 s com o jogador
parado no nascimento); antes, dois mapas ficavam 60 s sem contato. Commits `9af7650` e
`c8c227f`, 213 testes.

## Detalhe
- **A grade ja e o grafo.** Busca em largura sobre ~700 celulas custa menos que manter uma
  rede de waypoints a mao. Diagonal so quando os dois lados ortogonais estao livres, senao o
  bot corta a quina e entra na parede.
- **Guardar o destino e o que separa procurar de vagar.** Primeira versao sorteava destino
  novo a cada recalculo (1,5 s): o bot andava o tempo todo e nunca chegava a lugar nenhum -
  a medicao continuou em "nunca" nos mapas grandes. Com destino guardado, o escritorio
  resolveu na hora.
- **Ronda tem de ter proposito.** Mesmo com destino guardado, o de_inferninho seguia sem
  contato: o jogador estava num quarto de 3 celulas em ~480, e um sorteio uniforme quase
  nunca cai la. Uma em cada tres rondas vai ao **nascimento do inimigo** (como bot de CS que
  avanca para o sitio) e o resto escolhe o mais distante entre tres sorteios.
- **Memoria e ouvido:** vai ao ultimo lugar onde viu o jogador; tiro a ate 16 celulas manda
  quem esta perto para la. Sem isso, jogador parado e silencioso e invisivel.
- **Medir antes e depois** foi o que guiou tudo: um teste temporario imprimindo "1o dano em
  X s" por cenario e semente. Virou teste permanente (um por cenario, 60 s de limite).

## Relacionado
- [[RagdollGames]]
- [[2026-09-11-counter-ragdoll-bot-na-cobertura]]
- [[raycasting-em-flutter]]
