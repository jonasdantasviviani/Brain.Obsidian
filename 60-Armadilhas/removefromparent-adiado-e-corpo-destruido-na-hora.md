---
tipo: armadilha
titulo: removeFromParent do Flame e adiado, destroy da Box2D e na hora - o jogo trava no primeiro capotamento
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/armadilha, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [removeFromParent, destroy, Body, forge2d, box2d v3, memory access out of bounds, wasm, reinicio, capotamento, passo fixo, use after free, componente, updateTree]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: alta
---

# removeFromParent adiado, corpo destruido na hora
## Resumo
Trocar um carro no meio da corrida com `componente.removeFromParent(); corpo.destroy();` dentro
do passo de fisica trava o jogo: o componente ainda faz `update` naquele quadro e le a pose de um
corpo destruido - `RuntimeError: memory access out of bounds` no WebAssembly da Box2D.

## Contexto
`need-for-ragdoll`, 2026-09-21, a primeira vez que o jogo rodou numa tela (build baixado do CI).
Largada ok; os carros encostam, o motorista sai pela janela, o reinicio recria carro e boneco, e o
quadro seguinte congela. Nenhum teste pegava: o dominio era todo verde e a Box2D nunca tinha rodado.

## Detalhe
- **Flame**: `removeFromParent()` so marca; a remocao acontece no `processLifecycleEvents` do
  quadro seguinte. O `World.update` (onde roda o passo fixo) acontece ANTES do `update` dos filhos
  do mundo no mesmo `updateTree` - entao o `CarroComponent` velho ainda roda uma vez.
- **Box2D v3 (forge2d 0.15)**: `Body` e um id; ler `position` de um id destruido nao lanca
  excecao em Dart, le memoria liberada (asserts desligados no build web).
- Segundo caminho do mesmo erro: reposicionar um rival **dentro** do `for (piloto in _pilotos)` do
  passo; a variavel do laco segue apontando para o piloto destruido.

### Correcao que resolve todos os leitores atrasados
O dono do corpo guarda a ultima pose ao destruir e nunca mais toca a Box2D:
```dart
void destruir() {
  if (destruido) return;
  _poseFinal = PoseDoCarro(x: corpo.position.x, ..., velocidade: 0);
  corpo.destroy();
}
PoseDoCarro get pose => _poseFinal ?? PoseDoCarro(x: corpo.position.x, ...);
Vector2 get posicao => _poseFinal == null ? corpo.position : Vector2(...);
```
e **ninguem fora da infraestrutura le `carro.corpo.position` direto** (grep `carro\.corpo\.`).

### Como identificar
- stack com funcao wasm de indice pequeno (`wasm-function[193]`): e a Box2D (~270 KB), nao o
  CanvasKit (indices na casa dos milhares);
- a tela congela num quadro e o cronometro para - `read_console_messages` com `onlyErrors`.

## Relacionado
- [[forge2d-e-box2d-v3]]
- [[forge2d-viewfinder-zoom-multiplica-meters-to-pixels]]
- [[junta-que-quebra-lendo-constraint-force]]
