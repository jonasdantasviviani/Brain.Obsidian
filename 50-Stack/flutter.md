---
tipo: stack
titulo: Flutter / Dart
projeto: [Heavy, ICook, Games]
stack: [flutter, dart]
tags: [tipo/stack, stack/flutter]
palavras-chave: [flutter, dart, mobile, android, ios, bloc, cubit, get_it, very_good_analysis, import_lint, apk, jogo, idle, flame]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-20
confianca: alta
---

# Flutter / Dart
## Resumo
Framework mobile dos dois apps; Flutter 3.44.9 (Dart 3.12) verificado nesta maquina.

## Detalhe

### Onde e usado
| Projeto | App | Plataformas |
| --- | --- | --- |
| [[Heavy]] | Heavy Drive (app do motorista) | so Android na V1 |
| [[ICook]] | app de receitas | iOS + Android |
| [[Games]] | jogos idle offline (so planejamento) | iOS + Android |
| [[RagdollGames]] | jogos ragdoll rabisco (so planejamento) | **navegador** + iOS + Android |

### Por que no Heavy
Renderizacao propria (tela identica em Android 8 de entrada), APK de ~8-12 MB, bom desempenho em
aparelho fraco. Requisito real: motorista com celular ruim, offline-first.

### Arquitetura (Heavy)
Mesmas quatro camadas do back-end: `domain · application · infrastructure · presentation`
+ `di` e `utils`. Bloc/Cubit para estado, `get_it` para injecao.
Skill obrigatoria: `mobile-architecture` (arquitetura) e `design-system-mobile` (visual).

### Qualidade
`flutter analyze` com `very_good_analysis` + `import_lint.yaml` **reprova import que atravessa
camada**.

> **Atencao com `import_lint 2.0`**: o `import_lint.yaml` solto e ignorado; a config vai no `analysis_options.yaml`. Quem reprova e o `dart run import_lint` com `severity: "error"` - o plugin so mostra `info` no `dart analyze` (e so com `diagnostics: import_lint: true`) e o `flutter analyze` nao mostra diagnostico de plugin. Ver [[import-lint-2-config-e-regra-silenciosa]] (2026-09-12).

### SDK local
`flutter` esta em `~/Documents/Repos/Flutter/flutter` e no PATH.
`flutter doctor` diz o que falta para rodar em aparelho (precisa do Android SDK).

### Em jogo
Idle, clicker e merge nao pedem engine - `AnimatedBuilder`, `CustomPainter`, `Draggable` e
`Ticker` cobrem tudo, e `flame` so entra em simulacao densa desenhada a cada tick.
Ver [[flutter-puro-sem-engine-nos-jogos]] e [[motor-idle-em-flutter]].

Fisica e outra historia: ragdoll pede `flame` + `flame_forge2d` (Box2D em Dart) -
ver [[ragdoll-em-flutter-com-forge2d]]. Para web, CanvasKit com orcamento de ~5 MB e
entrada abstraida (teclado + toque) desde o inicio.

### Testar sem abrir Flutter
Teste de dominio que nao importa nada de `package:flutter` roda com **`dart test`**, e a
diferenca e brutal: `dart test test/domain` termina em **1 segundo**, enquanto `flutter test`
monta o bundle de assets (inclusive compilar shader com o `impellerc`) antes de qualquer
teste - e e justamente ai que ele trava em maquina apertada
([[impellerc-morto-por-memoria-trava-build-flutter]]).

Para isso o teste precisa importar `package:test/test.dart`, nao `flutter_test` - e `test`
entra em `dev_dependencies`. Vale a pena manter `test/domain` assim de proposito: o dominio
fica testavel em milissegundos e continua rodando dentro do `flutter test` normal.

```bash
dart test test/domain   # dominio puro, 1 s
flutter test            # tudo, inclusive teste de widget
```

## Relacionado
- [[Heavy]]
- [[ICook]]
- [[Games]]
- [[RagdollGames]]
- [[arquitetura-dotnet-em-camadas]]
