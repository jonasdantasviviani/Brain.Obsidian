---
tipo: armadilha
titulo: No Forge2DViewfinder o zoom multiplica o metersToPixels - tela preta e memory access out of bounds
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [flame_forge2d, Forge2DViewfinder, metersToPixels, zoom, viewfinder.zoom, tela preta, memory access out of bounds, RuntimeError, wasm, canvaskit, escala, camera, onGameResize]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# No Forge2DViewfinder o zoom multiplica o metersToPixels
## Resumo
Em `flame_forge2d 0.20`, `camera.viewfinder.zoom` e aplicado **por cima** de `metersToPixels`.
Escrever `zoom = pixelsPorMetro` eleva a escala ao quadrado: a camera mostra um centimetro de
mundo, a tela fica preta e o navegador solta `RuntimeError: memory access out of bounds`.

## Contexto
`ragdoll-go`, 2026-09-12. Para fixar a altura visivel em metros fiz, no `onGameResize`,
`camera.viewfinder.zoom = size.y / alturaVisivel` (238) com o jogo criado a
`metersToPixels: 120`. Escala real: 238 x 120 = **28.560 px/m**.

## Detalhe

### Como aparece
- Fundo **preto** (nem o `backgroundBuilder` aparece), so o HUD da viewport desenha.
- Console: `RuntimeError: memory access out of bounds at wasm://wasm/...`.
- O jogo roda **um** `update` e para: o crash e no **render** do primeiro quadro.

### Por que engana
O stack aponta para WASM, e num jogo com forge2d a suspeita obvia e a Box2D. **Nao era.**
O CanvasKit tambem e WASM: o que estourou foi ele, desenhando traco de caneta com espessura e
coordenadas milhares de vezes maiores que o normal. Custou uma rodada de `print` por etapa
(onLoad, cada passo do update) ate ver que o update inteiro passava.

### A fonte
```dart
// flame_forge2d-0.20.0/lib/forge2d_viewfinder.dart
double get zoom => super.zoom / _metersToPixels;
set zoom(double value) => super.zoom = value * _metersToPixels;
```

### O certo
```dart
@override
void onGameResize(Vector2 size) {
  super.onGameResize(size);
  if (size.x <= 0 || size.y <= 0) return;   // o setter tem assert > 0
  metersToPixels = math.max(size.y / alturaVisivel, size.x / larguraMaxima);
}
double get larguraVisivel => size.x / metersToPixels;   // nunca / zoom
```
- `zoom` fica em 1 - ele e para aproximar e afastar a camera, nao para escala.
- Quem converte espessura de traco (`metrosPorPixel`) le `1 / game.metersToPixels` **a cada
  quadro**, e nao guarda no construtor: a escala muda quando a tela gira.
- Papel pintado **dentro do mundo** tambem, e nao so no `backgroundBuilder` - atras da cena o
  que sobra e o preto do canvas.

### O sinal que denuncia
Tela preta + HUD de viewport desenhando normal = o **mundo** esta fora de escala ou fora de
quadro. Conferir `metersToPixels * zoom` antes de culpar a fisica.

## Relacionado
- [[forge2d-e-box2d-v3]]
- [[verificar-jogo-no-navegador-do-painel]]
- [[RagdollGames]]
