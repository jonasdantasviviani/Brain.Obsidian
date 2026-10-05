---
tipo: armadilha
titulo: Dependabot mergeado sem CI subiu pacote que o Flutter local nao aceita - pub get quebrou na main
projeto: [RagdollGames]
stack: [flutter, dart, github-actions]
tags: [tipo/armadilha, stack/flutter, git/github]
palavras-chave: [dependabot, very_good_analysis 11, meta 1.19, flutter_test, version solving failed, sdk constraint, dart 3.13, pub get, ci, merge sem ci]
origem: claude-code
criado: 2026-09-22
atualizado: 2026-09-22
confianca: alta
---

# Dependabot sobe pacote que o SDK local nao aceita
## Resumo
Tres PRs do Dependabot mergeados antes de o repo ter CI deixaram a main sem `pub get` possivel com
o Flutter da maquina (3.44.9, Dart 3.12.2).

## Detalhe
- `very_good_analysis 11.0.0` exige **Dart ^3.13** (`version solving failed ... requires SDK version ^3.13.0`).
- `meta 1.19.0` briga com o `flutter_test`, que **fixa `meta` exato** na versao do SDK (1.18.0).
- `hive_ce 2.20` era inofensivo (aceita Dart 3.4).
- Correcao (need-for-ragdoll PR #7): voltar `very_good_analysis ^10.3.0` e `meta ^1.18.0`, e no
  `.github/dependabot.yml` ignorar o major do lint e a `meta` (versao decidida pelo SDK). CI com
  `flutter-version` fixo = o da maquina, para os dois acusarem o mesmo.
- Conferir requisito de SDK antes de aceitar bump:
  `curl -s https://pub.dev/api/packages/<pkg> | python3 -c "import json,sys;[print(v['version'],v['pubspec'].get('environment')) for v in json.load(sys.stdin)['versions'][-3:]]"`

## Relacionado
- [[dependabot-mergeado-com-ci-vermelho-quebra-a-main]]
- [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]
