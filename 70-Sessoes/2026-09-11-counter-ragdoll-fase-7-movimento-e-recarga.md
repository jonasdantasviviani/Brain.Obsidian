---
tipo: sessao
titulo: Counter-Ragdoll - Fase 7 (recarga, reserva, agachar, andar, precisao)
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, dominio/jogos, jogo/fps]
palavras-chave: [recarga, municao de reserva, agachar, shift, espalhamento, mira dinamica, semiautomatica, faca, golpe forte, loja municao]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - Fase 7
## Resumo
"Pode continuar" no strike: fiz a Fase 7 do ROADMAP (movimento e combate fieis ao 1.6).
Commit `864bd99`, 155 testes, enviado para `RabiscoGames/counter-ragdoll`.

## Detalhe
- **Recarga com tempo + reserva:** `Arsenal` separa pente e reserva; `recarregar()` so comeca,
  as balas passam no fim; trocar de arma cancela. Arma nova vem com dois pentes de reserva.
  Caixa no chao e loja (B 6 / B 7) enchem a **reserva**.
- **Agachar = C, nao Ctrl:** no navegador **Ctrl+W fecha a aba** e W e andar - a pagina nao
  consegue impedir. Consequencia: o radio do 1.6 (Z X C) vai para Z X V.
- **Espalhamento:** `base x (1 + penalidadeDaArma x ritmo^2) x 0,6 agachado x 5 no ar`. O
  quadrado faz andar devagar custar pouco e correr custar muito. `ritmo` = distancia andada no
  passo / velocidade maxima, suavizado.
- **Mira dinamica:** vao da mira = espalhamento x (largura/2) / tan(fov/2) - o cone real na
  tela, sem numero magico.
- **Diagonal normalizada** (andava 41% mais rapido).
- **Semiautomatica:** a captura do painel mostrou a pistola esvaziando o pente com o gatilho
  preso (o painel as vezes nao entrega o pointerup). No 1.6 pistola e escopeta sao um tiro por
  clique - `Arma.automatica`.
- **Faca:** botao direito = golpe forte; `contextmenu` bloqueado no `MouseCru`. Pointer
  Events nao disparam `pointerdown` para o segundo botao se o primeiro ja esta preso.
- **Verificacao:** loja B 7 e reserva conferidas no navegador; agachar e mira dinamica por
  foto de cena ([[foto-de-cena-em-teste-flutter]]), ja que o painel nao segura tecla.

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[2026-09-11-counter-ragdoll-bala-com-altura-e-bots]]
