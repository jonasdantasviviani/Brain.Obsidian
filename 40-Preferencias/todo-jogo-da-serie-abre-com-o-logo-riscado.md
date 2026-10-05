---
tipo: preferencia
titulo: Todo jogo da Rabisco abre com o logo sendo riscado, e so depois se apresenta
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/preferencia, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [abertura, splash, logo, identidade de estudio, barra de progresso, frases engracadas, piada, carregando, som de desenho]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Todo jogo da Rabisco abre com o logo riscado
## Resumo
Jonas quer **sempre**, em todos os jogos: folha em branco -> logo da Rabisco sendo desenhado com
som de caneta -> a imagem do jogo com uma barra enchendo e **frases engracadas sobre o jogo** por
cima dela.

## Contexto
Pedido em 2026-09-17, sem jogo especifico: "Para os jogos do rabisco games, gostaria de sempre no
inicio do jogo iniciar com o logo da rabisco sendo desenhado na tela em branco, com som de
desenho". No dia seguinte reforcou pelo Counter-Ragdoll: "vai aparecer o logo da rabisco games
sendo desenhado e dps do fade ira aprecer o logo do counter ragdoll carregando com frases".

## Detalhe
- E **identidade de estudio**, nao enfeite de um jogo: mesmo ritmo em todos (constantes em
  `RitmoDaAbertura`). Nao encurtar num jogo so.
- **O cartaz do jogo dura 20 s** (pedido dele em 2026-09-18: "esta muito rapido, pode colocar 20
  segundos, para ter mais frases"), com a frase trocando a cada 2,5 s - **oito frases por jogo**
  cobrem o tempo sem repetir. Total da abertura: ~23,6 s, com toque na tela adiantando para o fim.
- **A capa ocupa a tela**, e a barra fica por cima dela, no pe - nada de cartao com moldura no
  meio da tela. Inteira, sem recorte: cortar as bordas come o letreiro do jogo.
- **O cartaz nao repete o som do logo** ("retire tambem o som da capa do jogo, ou coloque outro e
  nao o mesmo do logo"): so um tique seco de papel a cada troca de frase.
- O exemplo de frase que ele deu: *"desenhando armas, rabiscando bonecos"*. A voz e de quem esta
  rabiscando o jogo na hora; **nunca "Carregando..."**.
- "Tela em branco" no pedido = a **folha** (`#F4F1E8`). Branco puro mata a ilusao de papel, e a
  [[RagdollGames]] ja decidiu isso na identidade - seguir a identidade.
- A imagem do jogo e a **capa** que ele mesmo desenhou (as do hub, em `img/jogos/<slug>.jpg`).
- Cada jogo escreve as **suas** frases; a serie tem uma lista de reserva.

Como esta feito: [[abertura-de-estudio-com-logo-riscado]]. Instalado nos tres jogos com codigo em
2026-09-18 ([[2026-09-18-rabisco-abertura-dos-jogos]]).

## Relacionado
- [[RagdollGames]]
- [[abertura-de-estudio-com-logo-riscado]]
- [[Rabisco-Hub]]
