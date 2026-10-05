---
tipo: armadilha
titulo: Build Flutter morre sem explicacao quando a memoria aperta - o morto e o impellerc
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [impellerc, ShaderCompilerException, exit code -9, sigkill, flutter build web, flutter run, memoria, travado, ink_sparkle, shader]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Build Flutter morre sem explicacao quando a memoria aperta
## Resumo
`ShaderCompilerException ... failed with exit code -9` nao e erro de shader nem de Rosetta: e o
`impellerc` levando SIGKILL por pressao de memoria.

## Contexto
No meio da sessao de 2026-09-20 o `flutter run -d web-server` parou de subir. Antes disso, na
mesma sessao e no mesmo projeto, ele tinha subido e servido o jogo por vinte minutos.

## Detalhe

### Os tres sintomas, todos do mesmo problema
1. `Oops; flutter has exited unexpectedly: "ShaderCompilerException: Shader compilation of
   ink_sparkle.frag ... failed with exit code -9"`;
2. `The Dart compiler exited unexpectedly.` no `flutter test --platform chrome`;
3. pior de todos: **processo vivo em estado `SN`, log de zero byte, nada acontecendo** - nenhum
   erro, so travado.

### O que enganou no diagnostico
`flutter doctor` mostra, nesse estado:
```
✗ Downloaded executables cannot execute on host.
  Flutter requires the Rosetta translation environment on ARM Macs.
```
**Nao e isso.** Conferir antes de sair instalando Rosetta:
```bash
file $(find $FLUTTER/bin/cache/artifacts/engine -name impellerc | head -1)
# Mach-O 64-bit executable arm64      <- ja e nativo
$IMP --help; echo $?                  # 0: executa sem problema nenhum
ls /Library/Apple/usr/share/rosetta   # e o Rosetta esta instalado
```
O binario e arm64 e roda; o que ele nao consegue e **viver** enquanto o `dart2js` ocupa a
memoria toda.

### Como confirmar
```bash
vm_stat | head -4     # "Pages free" na casa dos milhares = ~60 MB livres
```

### O que fazer
- fechar o que estiver pesado antes do build (navegador com muitas abas, Spotify, outro
  `flutter run`), e **nunca dois comandos Flutter ao mesmo tempo** - `flutter analyze` junto de
  um `flutter run` ja passou de 4 minutos numa maquina de 16 GB;
- matar restos antes de tentar de novo: `pkill -9 -f flutter_tools`;
- se ja existe build em `build/web`, servir o build velho e uma verificacao **de outra
  versao** - nao vale como verificacao da mudanca de agora.

> **Correcao (2026-09-21):** a causa raiz nao era so memoria. O simulador do iOS esquecido
> ligado ocupava ~2 GB, e o SDK/projeto em `~/Documents` estavam evictados pelo iCloud com o disco
> a 98%. Ver [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]. O conselho de "fechar Chrome e
> Spotify" abaixo nao era o que resolvia.

## Relacionado
- [[http-server-do-python-serve-build-velho]]
- [[cadeia-com-pipe-esconde-falha-de-build]]
- [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]
