---
tipo: armadilha
titulo: Abertura com som no navegador corre muda - o audio espera o primeiro gesto
projeto: [RagdollGames]
stack: [flutter, dart, web]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [autoplay, audiocontext suspended, gesto do usuario, splash com som, abertura muda, resume, som no navegador]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Abertura com som no navegador corre muda
## Resumo
A abertura comeca no primeiro quadro do app; o navegador so libera audio **depois de um gesto**.
Sem tratar, o logo e desenhado em silencio e o som de caneta nunca toca na primeira visita.

## Contexto
Pedido da [[todo-jogo-da-serie-abre-com-o-logo-riscado]]: som de caneta enquanto o logo e
riscado. Mas `AudioContext` criado antes de qualquer toque nasce `suspended` (Chrome, Safari e
Firefox) - e o `resume()` fora de um gesto nao resolve.

## O que foi feito
1. O contexto e criado na abertura mesmo assim (no aparelho ele ja nasce tocando; no navegador
   fica suspenso, e os nos de audio ja ficam ligados toca o vazio).
2. O primeiro `onPointerDown` chama `desbloquear()`, que da `resume()` - o risco entra **no meio
   do desenho**, de onde estiver.
3. Enquanto `precisaDeToque` for verdade, a folha mostra *"toque para ouvir a caneta"* no pe.
4. **O toque durante o desenho nao pula nada.** Se pulasse, quem tocou para ouvir perderia
   exatamente o que queria ver. Adiantar so vale depois do logo pronto.

## Detalhe
- `_contexto?.state != 'running'` e o teste certo: `state` e `'suspended'` antes do gesto e
  `'running'` depois. `_contexto != null` nao diz nada.
- Fora da web o esboco silencioso devolve `precisaDeToque == false` - senao a dica de toque
  apareceria para sempre no celular, onde som nao existe ainda.
- Mover o mouse **nao** conta como gesto: em desktop sem clique, a abertura fica muda.

## Relacionado
- [[abertura-de-estudio-com-logo-riscado]]
- [[som-sintetizado-com-web-audio]]
- [[RagdollGames]]
