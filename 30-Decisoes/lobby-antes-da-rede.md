---
tipo: decisao
titulo: Lobby de multijogador antes da rede, atras de uma interface
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/decisao, dominio/jogos, arquitetura, multijogador]
palavras-chave: [multijogador, lobby, sala, ServicoDeSalas, interface, LAN, servidor autoritativo, codigo da sala, costura]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# Lobby de multijogador antes da rede

## Resumo
O Counter-Ragdoll ganhou menu, sala e lobby **sem rede nenhuma**: a `Sala` e so o acordo (mapa,
bots, times, pronto) e quem leva isso adiante e a interface `ServicoDeSalas`. Hoje so existe
`SalasNaMemoria`; LAN e servidor entram como outras implementacoes, sem mexer no menu.

## Contexto
Jonas quer LAN e online, mas pediu para comecar pelo menu e criacao de sala e "depois ver como
publicar". Escolher transporte agora (WebSocket, descoberta UDP, servico de terceiros) seria decidir
hospedagem e seguranca antes de haver jogo em rede para testar.

## Detalhe
- Codigo de sala com 4 caracteres sem O/0, I/1, S/5: e para ser dito em voz alta.
- Gente de verdade tira bot da vaga; o anfitriao digitando o proprio codigo so volta para a sala.
- O servico mora no `TelaPrincipal`, nao no menu: o menu e desmontado a cada partida.
- A tela diz o que ainda nao e verdade: salas so nesta maquina; a partida ignora os times da sala.

## Trade-off
- Ganho: lobby inteiro escrito e testado agora (unidade + widget), sem servidor.
- Custo: a interface foi desenhada sem conhecer o transporte; `abrir` pode precisar devolver o codigo
  gerado pelo servidor e as mudancas podem virar stream em vez de `anunciar`.
- Quando for para a internet: servidor autoritativo e as 28 regras de [[80-Seguranca]].

## Atualizacao 2026-09-12 - transporte escolhido
Jonas: "rede local por enquanto". Plano: servidor Dart puro (`dart:io`) numa maquina da rede, servindo o
build web por HTTP e o lobby por WebSocket na mesma origem (na web nao ha socket cru nem broadcast UDP,
entao a descoberta e "abra o endereco do anfitriao"). O servidor e a autoridade do lobby; a partida
sincronizada vem numa segunda etapa, depois de o dominio suportar varios combatentes com time (bots
aliados), com o servidor rodando a partida e os clientes mandando entrada.

## Relacionado
- [[2026-09-12-counter-ragdoll-capacete-placar-cenarios-lobby]]
- [[RagdollGames]]
- [[tecnologia-mais-chata-que-resolve]]
