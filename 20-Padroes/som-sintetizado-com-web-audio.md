---
tipo: padrao
titulo: Som de jogo sintetizado com Web Audio - sem arquivo de audio
projeto: [RagdollGames]
stack: [flutter, dart, web]
tags: [tipo/padrao, stack/flutter, dominio/jogos]
palavras-chave: [som, audio, web audio, sintese, ruido, oscilador, filtro, espacial, pan, autoplay, passos, tiro]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: media
---

# Som sintetizado com Web Audio
## Resumo
Tiro, passo, faca, clique e musica **montados na hora** com ruido, filtro e oscilador - nenhum
arquivo de audio, nenhuma licenca, e o timbre lo-fi combina com desenho a caneta.

## Contexto
`counter-ragdoll`, 2026-09-10, em `lib/infrastructure/som/`. Web Audio vem pronto no pacote
`web` (`AudioContext`, `OscillatorNode`, `GainNode`, `BiquadFilterNode`, `StereoPannerNode`,
`AudioBufferSourceNode`). **Confianca media**: implementado e sem erro no console, mas o
painel de teste nao permite ouvir - os timbres ainda nao foram conferidos de ouvido.

## Detalhe

### Duas pecas fazem quase tudo
1. **Chiado**: um segundo de ruido branco gerado uma vez, tocado por um filtro (passa-baixa,
   passa-banda) com envelope que cai rapido. Tiro, passo e faca sao isso com numeros
   diferentes. Comecar num ponto sorteado do ruido faz dois tiros seguidos nao soarem iguais.
2. **Tom**: oscilador com a frequencia caindo. O "baque" grave do tiro, o estalo de recarga, o
   "tuc" de acerto.

### Som espacial sem motor 3D
Para cada fonte (passo ou tiro de bot):
- volume = `(1 - distancia / alcance)^2`
- **abafado atras de parede**: volume x 0,45 se nao ha linha de visao - reaproveita o raio do
  proprio jogo
- lado = `sin(anguloAteAFonte - anguloDaMira)` num `StereoPannerNode`

Tiro de longe tambem fica **menos agudo** (corte do filtro cai com o volume): distancia se
ouve no timbre, nao so na altura.

### Regras do navegador
- Audio so nasce depois de um **gesto do usuario**: criar o `AudioContext` dentro do primeiro
  clique (`desbloquear()`), e nao na abertura. Antes disso, tudo e silencio sem erro.
- Musica so no menu: na partida ela cobre os passos, que sao informacao de jogo.

### Plataforma
Import condicional com esboco silencioso fora da web - no celular o jogo roda mudo ate ter
um motor nativo.

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
