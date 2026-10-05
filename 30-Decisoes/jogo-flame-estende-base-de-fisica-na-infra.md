---
tipo: decisao
titulo: O jogo Flame fica na apresentacao e estende uma base de fisica na infra
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/decisao, stack/flutter, dominio/jogos]
palavras-chave: [JogoComFisica, Forge2DGame, camadas, presentation, infrastructure, import_lint, forge2d_so_na_infra, criarChao, arquitetura, HasGameReference, ciclo]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# O jogo Flame estende uma base de fisica na infra
## Resumo
`presentation/jogo/<jogo>_game.dart` continua sendo o jogo, mas estende
`infrastructure/fisica/jogo_com_fisica.dart` - assim nenhum tipo do Forge2D aparece na apresentacao
e a regra `forge2d_so_na_infra` vale **sem excecao**.

## Contexto
Decisao do Jonas em 2026-09-12, ao ligar o `import_lint` de verdade no `need-for-ragdoll` e no
`ragdoll-go`. A unica violacao de cada jogo era o `*_game.dart` importar `flame_forge2d` para
estender `Forge2DGame<MundoFisico>` e criar o chao (`BodyDef`, `Polygon.box`, `ShapeDef`).

## Detalhe
```dart
// infrastructure/fisica/jogo_com_fisica.dart
abstract class JogoComFisica extends Forge2DGame<MundoFisico> {
  JogoComFisica({required super.metersToPixels, Vector2? gravidade})
    : super(world: MundoFisico(gravity: gravidade));

  void criarChao({required double x, required double y, required double meiaLargura,
                  double meiaAltura = 0.2, double atrito = 0.9}) { /* createBody... */ }
}

// presentation/jogo/ragdoll_go_game.dart - importa so flame/components (Vector2) e a base
class RagdollGoGame extends JogoComFisica { /* criarChao(x: 10, y: 0.2, meiaLargura: 22, atrito: 0.85) */ }
```
Membros herdados (`world`, `camera`, `metersToPixels`) seguem usaveis sem importar o Forge2D; o que a
apresentacao perde e so a capacidade de **nomear** tipo da Box2D - que e exatamente a regra.

### Alternativas descartadas
| Alternativa | Por que nao |
| --- | --- |
| Excecao para `*_game.dart` no alvo da regra | `BodyDef`/`Polygon` ficam na apresentacao, e excecao por nome de arquivo e fragil. O `import_lint.yaml` de 09-09 ja afrouxava assim (lista branca so com `caneta.dart`) - e ninguem percebeu |
| Mover o jogo inteiro para `infrastructure/` | Ciclo: `HudComponent`, `BichoComponent` e `BolinhaComponent` usam `HasGameReference<RagdollGoGame>` |

### Regra para os proximos jogos
Corpo novo criado pelo jogo (parede, rampa, pista) vira metodo na `JogoComFisica` ou classe em
`infrastructure/fisica` - nunca `createBody` na apresentacao.

## Relacionado
- [[RagdollGames]]
- [[import-lint-2-config-e-regra-silenciosa]]
- [[ragdoll-em-flutter-com-forge2d]]
