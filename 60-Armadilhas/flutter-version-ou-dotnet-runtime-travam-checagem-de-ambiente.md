---
tipo: armadilha
titulo: Checar ambiente - flutter --version trava e SDK .NET novo nao roda net9
projeto: [Eden]
stack: [flutter, dotnet]
tags: [tipo/armadilha, stack/flutter, stack/dotnet]
palavras-chave: [flutter --version travou, dart sdk ^3.13, net9.0 runtime ausente, dotnet --list-runtimes, timeout macos]
origem: claude-code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: media
---

## Resumo
Ter o SDK instalado nao basta: confira a versao do Dart exigida pelo pubspec e os runtimes .NET, nao so o SDK.

## Contexto
Validando o ambiente do Eden. `flutter --version` passou de 2 min sem responder (provavel download/atualizacao de engine); `timeout` nao existe no macOS.

## Detalhe
- Ler o Dart direto: `<flutter>/bin/cache/dart-sdk/version` e comparar com `environment.sdk` do `pubspec.yaml`.
- `dotnet --list-runtimes` mostra se o `TargetFramework` do projeto (net9.0) tem runtime; SDK 10 compila net9, mas nao executa sem runtime 9.
- Nao use `timeout` (inexistente); rode em background e leia o arquivo de saida.

## Relacionado
[[dependabot-sobe-pacote-que-o-sdk-local-nao-aceita]] · [[2026-10-01-eden-validacao-ambiente]]
