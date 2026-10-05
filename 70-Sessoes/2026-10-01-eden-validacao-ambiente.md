---
tipo: sessao
titulo: Eden - validacao do ambiente da maquina
projeto: [Eden]
stack: [dotnet, flutter, docker, ollama, node]
tags: [tipo/sessao, stack/dotnet, stack/flutter, stack/docker]
palavras-chave: [setup, ambiente, instalar, flutter pubspec sdk, net9 runtime, ollama qwen3, docker daemon, .env]
origem: claude-code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: alta
---

## Resumo
Auditoria somente-leitura do ambiente para rodar o Eden: 7 pendencias, nada alterado.

## Contexto
Jonas pediu para validar se tudo que precisa ser instalado/configurado ja estava feito. Nenhum arquivo foi editado.

## Detalhe
Estava ok: .NET SDK 10.0.302, Node 26, npm, git, gh, Python 3, Docker 29.6, Xcode, Android Studio + SDK, Flutter (stable) em `~/Documents/Repos/Flutter/flutter`, binario do Ollama.

Pendencias encontradas:
1. `Directory.Build.props` usa `net9.0`, mas so ha runtime .NET 10 -> instalar .NET 9 ou migrar para `net10.0`.
2. Flutter local com Dart 3.12.2; `mobile/pubspec.yaml` pede `^3.13.5` (README: Flutter 3.47+) -> `flutter upgrade`. README cita `/opt/flutter/bin`, que nao existe aqui.
3. Docker Desktop fechado (daemon fora).
4. Ollama sem servidor; so `gemma4` baixado, projeto pede `qwen3:8b`.
5. Sem `.env` (precisa `EDEN_JWT_KEY` e `EDEN_ADMIN_PASSWORD_HASH`).
6. Sem `psql` (make seed-demo) nem CocoaPods (iOS).
7. `web/node_modules` vazio; Android SDK sem `cmdline-tools`.

Comando util: no macOS nao existe `timeout`; use sleep/background.

## Relacionado
[[criar-env-a-partir-do-example-no-script-de-setup]] · [[env-nunca-foi-commitado-clone-novo-nao-roda]] · [[dependabot-sobe-pacote-que-o-sdk-local-nao-aceita]]

## Atualizacao - migracao para .NET 10 (mesmo dia)
- Pedido do Jonas: trocar para .NET 10. Feito: `net10.0` em Directory.Build.props, Dockerfile (sdk/aspnet 10.0), CI (10.0.x).
- Pacotes: EF/AspNetCore 10.0.12, Npgsql EF e Serilog.AspNetCore 10.0.0, OpenTelemetry 1.19.x, Testcontainers 4.15.0 (os antigos tinham vulnerabilidade conhecida NU1902/3/4 e TreatWarningsAsErrors quebrou o build).
- Analisadores novos: CA1875 (Regex.Count), CA1873 (log com IsEnabled), PostgreSqlBuilder(imagem).
- OpenAPI do .NET 10 sai 3.1 e numeros como `integer|string`; fixado em 3.0 + SchemaTransformer que remove o `string` -> contrato quase igual, `npm run gen` e tsc ok.
- Resultado: 378 testes passando; next build ok; check_contract do mobile ok.
- Feito tambem: `.env` com JWT gerado, Docker/Ollama abertos, `npm install`. `qwen3:8b` baixando (disco com ~3 GB livres).
- Pendente (Jonas): hash da senha admin, Flutter fora do iCloud + upgrade, cmdline-tools Android.
