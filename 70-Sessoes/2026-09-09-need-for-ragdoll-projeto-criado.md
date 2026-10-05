---
tipo: sessao
titulo: Need for Ragdoll - projeto criado e motor ragdoll rodando
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [flutter create, flame, forge2d, box2d v3, ragdoll, junta, revolute, passo fixo, very_good_analysis, import_lint, web]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: alta
---

# Need for Ragdoll - projeto criado
## Resumo
Primeiro jogo da serie [[RagdollGames]] saiu do papel: projeto Flutter criado, motor ragdoll
com 11 corpos rodando, `flutter analyze` limpo e 11 testes de dominio passando.

## Contexto
Executado o `producao/00-como-comecar.md` escrito na sessao anterior. Ambiente:
Flutter 3.44.9, Dart 3.12.2.

## Detalhe

### Onde fica
`~/Documents/Repos/Games/need-for-ragdoll`

### Versoes que o pub resolveu
`flame ^1.38.2` · `flame_forge2d ^0.20.0` · `forge2d 0.15.1` · `flutter_bloc ^9.1.1` ·
`get_it ^9.2.1` · `hive_ce ^2.19.3` · `very_good_analysis ^10.3.0` · `import_lint ^2.0.0`

### A descoberta que muda tudo: Forge2D agora e Box2D v3
`flame_forge2d 0.20` / `forge2d 0.15` **nao sao mais a Box2D 2.x portada para Dart** - sao um
binding para a **Box2D v3 nativa**, com WASM no navegador. Codigo e tutorial antigos do
flame_forge2d nao compilam. Ver [[forge2d-e-box2d-v3]].

### Como ficou
| Camada | O que tem |
| --- | --- |
| `domain` | `Esqueleto.humano()` (11 ossos, 10 juntas), `Consciencia`, `Ponto`, `Angulo` - sem Flutter e sem Forge2D |
| `application` | `PassoFixo` - acumulador de passo fixo com teto por frame |
| `infrastructure/fisica` | `MundoFisico` (passo fixo) e `CorpoRagdoll` (unico lugar que conhece Box2D) |
| `presentation` | `Caneta` (traco tremido), `RagdollComponent`, `PapelComponent`, tokens |

### Qualidade
`flutter analyze` com `very_good_analysis` **limpo**. 11 testes de dominio passando, rodando
em milissegundos porque `domain` nao importa Flutter.

Os testes que valem: junta com limite valido (o que separa ragdoll de macarrao), joelho que so
dobra para tras, e **o total simulado independe do tamanho do frame** - a prova do passo fixo.

### Verificado no navegador
`flutter build web --release` passa (1,6 MB de `main.dart.js`; o primeiro build leva ~18 min, os
seguintes ~10 s). Servido em `localhost`, aparece: folha pautada com margem, chao riscado e o
**boneco em pe desenhado a caneta com tremor**. O toque empurra e gira.

### O que ainda nao esta bom
- **O boneco nao tomba.** Em pe, joelho e quadril ficam encostados no proprio limite e as
  canelas sao caixas largas: o conjunto vira pilha estavel que se auto-endireita. Falta osso de
  pe e limite mais folgado no estado solto. E ajuste de feel - dias 5 e 7 do sprint.
- A margem vermelha saiu de quadro quando a folha cresceu para 60x40 m.
- `forge2d` pede `box2d.wasm` em dois caminhos errados (404) antes de acertar em
  `assets/packages/forge2d/...`. Funciona, mas suja o console.

### O que ainda nao existe
Carro, pista, corrida. Isso e do dia 6 em diante do sprint.

## Relacionado
- [[RagdollGames]]
- [[forge2d-e-box2d-v3]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[impulso-em-corpo-articulado]]
