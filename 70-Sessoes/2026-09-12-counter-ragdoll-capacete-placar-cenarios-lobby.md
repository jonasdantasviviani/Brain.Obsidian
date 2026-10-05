---
tipo: sessao
titulo: Counter-Ragdoll - capacete, placar no TAB, cenarios desenhados e lobby de multijogador
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/counter-ragdoll]
palavras-chave: [capacete, colete, headshot, placar, TAB, porta, janela, piso, piscina, agua, raycasting, multijogador, sala, lobby, codigo da sala, ServicoDeSalas]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Counter-Ragdoll - capacete, placar no TAB, cenarios desenhados e lobby

## Resumo
Quatro pedidos numa leva: capacete que segura headshot (e aparece desenhado no bot), placar no
TAB igual ao CS, cenarios "realistas a caneta" (porta, janela, piso, piscina em que se entra) e o
comeco do multijogador (menu, criar sala, lobby). Cinco commits em `RabiscoGames/counter-ragdoll`,
268 testes verdes.

## Pedido
Jonas: multijogador em LAN e online vira meta ("ja pode ir fazendo o menu, a criacao de sala; depois
vemos como publicar"); **capacete segura HS mas toma dano**; desenhar colete e capacete no bot;
TAB mostrando mortos e vivos como no CS; e "as fases estao muito iguais, so tracos com comodos -
se for porta, o desenho da porta; se for piscina, o desenho dela e que de para entrar".

## O que foi feito
- `6655dc8` **capacete e colete**: `Protecao` e a unica regra de dano, usada por bot e jogador.
  Cabeca sem capacete mata; com capacete o dano passa x2 e o capacete se parte. Bots tambem miram
  na cabeca (2% a 20% dos acertos, pela dificuldade). Equipamento dos bots sai da dificuldade sem
  sorteio. Desenho: cupula com aba e placa peitoral com alcas, caneta preta sobre o boneco vermelho.
  Loja ganhou "Colete e capacete" ($1.000). Mira escreve "capacete" quando ele segurou.
- `345807d` **placar no TAB**: `Tabela`/`Ficha` no `Confronto` (abate e morte da partida inteira;
  vivo e da rodada). Bots ganharam nomes de papelaria (Nanquim, Estilete...). `Configuracoes.nome`.
- `9f31bee` **cenarios desenhados**: porta `D` (abre sozinha, desenhada com almofadas), janela `%`,
  pisos `~ : " _ ;` com piso padrao por mapa, acabamento de parede por mapa (tijolo, reboco, chapa,
  bloco), agua que desacelera e afunda o olho. Os seis mapas reescritos e validados por flood fill.
- `59b691b` **multijogador (so o lobby)**: `Sala`, `CodigoDaSala`, interface `ServicoDeSalas` e a
  implementacao `SalasNaMemoria`; paginas Multijogador, Abrir sala, Salas abertas e Lobby.
- `d0707f1` ROADMAP com as quatro entregas e a secao nova de multijogador.

## Decisoes
- [[counter-ragdoll-capacete-segura-headshot]]
- [[lobby-antes-da-rede]]
- No normal os bots usam **so capacete**: colete dobraria a vida efetiva deles contra tiro no corpo.

## Verificacao
- Testes de dominio para protecao, tabela, portas, janela, agua, piso, sala e servico; widget test
  do lobby (abrir, ficar pronto, comecar) e do codigo errado.
- Cenas renderizadas em PNG ([[foto-de-cena-em-teste-flutter]]) para ajustar o desenho: a primeira
  versao da fiada de tijolo + hachura virou papel quadriculado; o chao por amostra geometrica errava
  a junta do azulejo - trocado por DDA.
- Navegador do painel voltou a funcionar: menu, Multijogador, Abrir sala e Lobby renderizam. Foi la
  que apareceu o bug do campo "Nome da sala" nascendo com o nome do jogador
  ([[flutter-estado-de-campo-reusado-entre-paginas]]); corrigido com chave e com teste (`093e92d`),
  e conferido de novo no navegador: o campo abre com "Sala nova".

## Pendencias
- LAN (descoberta e partida entre dois computadores), servidor autoritativo, publicar.
- "Comecar" roda a partida contra os bots do mapa: times e vagas do lobby ainda nao valem (dito na tela).
- Sons da fase 2 continuam sem ninguem ter ouvido.

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[2026-09-11-counter-ragdoll-mapa-do-que-falta]]
