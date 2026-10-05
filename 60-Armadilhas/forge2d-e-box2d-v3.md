---
tipo: armadilha
titulo: Forge2D 0.15 e Box2D v3 nativa - a API antiga nao existe mais
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [forge2d, box2d, flame_forge2d, api, fixture, createFixture, BodyComponent, joint, wasm, migracao]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-12
confianca: alta
---

# Forge2D 0.15 e Box2D v3 nativa
## Resumo
`flame_forge2d 0.20` / `forge2d 0.15` deixaram de ser Box2D 2.x em Dart puro e viraram binding
da **Box2D v3 nativa** (FFI no aparelho, WASM no navegador). Todo exemplo antigo quebra.

## Contexto
Encontrado ao criar o [[RagdollGames]] em 2026-09-09. Escrever de memoria teria custado uma
sequencia de erros de compilacao; ler o pacote em `~/.pub-cache` antes resolveu em 5 minutos.

## Detalhe

### O que mudou
| Antes (Box2D 2.x em Dart) | Agora (v3) |
| --- | --- |
| `body.createFixture(FixtureDef(shape))` | `body.createShape(ShapeGeometry, ShapeDef)` |
| `PolygonShape()..setAsBox(w, h)` | `Polygon.box(meiaLargura, meiaAltura)` |
| `world.createJoint(RevoluteJoint(def))` | `world.createRevoluteJoint(def)` - um metodo por tipo |
| `def.initialize(a, b, ancora)` | preencher `localAnchorA` / `localAnchorB` a mao |
| motor: `enableMotor` + `motorSpeed` | **mola**: `enableSpring`, `targetAngle`, `hertz`, `dampingRatio` |
| `world.stepDt(dt)` | `world.step(dt, subStepCount: n)` |
| atrito no fixture | `ShapeDef(material: SurfaceMaterial(friction: ...))` |

### As duas pegadinhas que custam tempo
1. **`Forge2DWorld` (do Flame) tem `createBody`, mas nao tem `createJoint`.** Junta so pelo
   mundo cru: `world.physicsWorld.createRevoluteJoint(def)`. O erro e
   `The method 'createRevoluteJoint' isn't defined for the type 'Forge2DWorld'`.
2. **Na web, o Box2D so existe depois de `await super.onLoad()`.** Criar corpo antes disso
   lanca em tempo de execucao, so no navegador - passa batido no teste e no aparelho.

### O ganho inesperado
A mola de junta da v3 (`enableSpring` + `targetAngle` + `hertz`) e **melhor** para ragdoll
ativo do que o motor da 2.x: basta multiplicar `springHertz` pela consciencia (0 a 1) e o
boneco vai de "em pe cambaleando" a "trapo no ar" com uma variavel so.

### Teste de fisica nao roda em `flutter test`
`flutter test` no host morre com **"Connection closed before test suite loaded"** assim que o
teste importa `flame_forge2d`: o isolate de teste nao tem a biblioteca nativa da Box2D. Teste
que toca fisica tem de viver em `integration_test/` e rodar com aparelho ou emulador
(`flutter test integration_test -d <aparelho>`).

Consequencia pratica: **o que precisa de teste rapido tem de morar em `domain`, puro**. Foi o
que salvou o `PassoFixo` e o `Esqueleto` - rodam em milissegundos porque nao importam nem
Flutter nem Forge2D.

### A licao
Antes de escrever contra um pacote que mudou de major, **ler a fonte em `~/.pub-cache`** -
`find ~/.pub-cache/hosted/pub.dev -maxdepth 1 -name "<pacote>-*"`. Custa 5 minutos e evita
uma hora de erro de compilacao.

## Relacionado
- [[RagdollGames]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[forge2d-viewfinder-zoom-multiplica-meters-to-pixels]] - camera: zoom por cima de metersToPixels
