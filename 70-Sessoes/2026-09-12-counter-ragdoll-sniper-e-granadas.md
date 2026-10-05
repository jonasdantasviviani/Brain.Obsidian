---
tipo: sessao
titulo: Counter-Ragdoll - sniper com luneta e as tres granadas
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/counter-ragdoll, raycasting]
palavras-chave: [sniper, luneta, zoom, awp, granada, explosiva, cegante, flashbang, fumaca, smoke, arremesso, quique, perfuraColete]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Counter-Ragdoll - sniper e granadas

## Resumo
Jonas: "continue o que falta" e, no meio, "esta sem sniper tambem". Segui o ROADMAP (o item 1, ouvir o
som, so ele fecha) pelo item sem dependencia - granadas - e puxei a sniper, que estava em
"fidelidade fina".

## Sniper
- `Arma.precisao` ("Sniper"): 115 de dano, `perfuraColete` 0,9 (um tiro no tronco derruba com colete),
  `luneta` [2,5, 6], `espalhamentoSemLuneta` 0,09. Loja B 4 2, $4.750; no chao do de_poeira e do galpao.
- Botao direito ou E alterna; o tiro guarda a luneta e ela volta quando o gatilho libera; trocar ou
  recarregar fecha. Mouse gira menos na luneta.
- Zoom: ver [[raycasting-em-flutter]] ("Zoom de luneta num raycaster").

## Granadas
- `TipoDeGranada` (pavio, maximo) e `Granada` (fisica de ponto eixo a eixo, com as faixas de altura do
  mapa: quica em parede, perde forca no chao, rola, para no tampo do balcao).
- Arsenal guarda granadas por tipo; a arma da granada entra e sai da lista; comprar nao troca a mao.
- Partida: `lancarGranada`, estouros (desenho + som), explosiva com linha de visao e queda linear,
  cegante com forca por distancia e por onde se olha, fumaca alimenta `CerebroDoBot.fumacas` e o bot
  deixa de ver atraves.
- Desenho: borrao de tinta, estrela de marca-texto, nuvem de bolhas na fila dos billboards; cegueira
  e uma folha em branco por cima de tudo, HUD inclusive.

## Verificacao
307 testes (11 da sniper, 17 das granadas). Cenas em PNG da sniper na mao e dos dois degraus da
luneta (a posicao de uma quina se afasta do centro 2,5x e 6x, conferido), das granadas na mao, do
estouro e da fumaca por fora e por dentro. No navegador, numa partida no `cs_escritorio`: B 8 5
comprou a fumaca, a tecla 2 pos na mao, o clique arremessou, a nuvem apareceu no corredor com um bot
desenhado na frente dela, e a granada saiu da lista com a pistola voltando para a mao. O painel
mudou de 800x600 para 800x791 no meio da partida - screenshot antes de cada clique.

## Tropeco
`dart analyze` ficou parado 5 minutos sem gastar CPU; ver [[dart-analyze-parado-sem-cpu]].
Desenho da sniper saiu embolado porque os blocos foram desenhados do mais perto para o mais longe.
`flutter build web` falhou duas vezes seguidas dentro de `ShaderCompiler.compileShader` (copia de
assets, nada do nosso codigo) e passou na terceira sem mudanca nenhuma: se o erro do build web for
nesse passo, rode de novo antes de procurar bug no Dart.

## Pendencias
Bot jogar granada; modo bomba; bots aliados; o lote pequeno do ROADMAP.

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[2026-09-12-counter-ragdoll-teto-e-subir-no-balcao]]
