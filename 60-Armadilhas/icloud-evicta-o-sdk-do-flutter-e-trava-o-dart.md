---
tipo: armadilha
titulo: iCloud evicta o SDK do Flutter em ~/Documents e o Dart trava, "trunca" arquivo e mata build
projeto: [RagdollGames]
stack: [flutter, dart, macos, icloud]
tags: [tipo/armadilha, stack/flutter, stack/macos]
palavras-chave: [icloud, dataless, evictado, optimize storage, disco cheio, dart analyze trava, File truncated, Operation timed out, index.lock, SIGKILL 247, impellerc, analysis server 0% cpu, simulador ligado, cpu load]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-26
confianca: alta
---

# iCloud evicta o SDK do Flutter e o Dart trava
## Resumo
Com o SDK do Flutter e o projeto dentro de `~/Documents` (sincronizado) e o disco quase cheio, o
macOS devolve os arquivos ao iCloud ("dataless"): toda leitura vira download, e o sintoma parece
**falta de memoria**, mas nao e.

## Contexto
Sessao de 2026-09-20/21 no `need-for-ragdoll`. Durante horas: `dart analyze` "travado com 0% de
CPU", `flutter build web` morto com `ShaderCompilerException ... exit code -9`, `dart test` com
`File truncated before or at offset 0x0`, `git` com `Operation timed out` e `index.lock` de 0
byte. Eu diagnostiquei como memoria e aconselhei fechar Chrome e Spotify. **Estava errado nos dois
pontos** - ver [[impellerc-morto-por-memoria-trava-build-flutter]], que descreve so um sintoma.

## Detalhe

### Duas causas empilhadas
1. **Simulador do iOS esquecido ligado** (`xcrun simctl boot` numa sessao anterior): ~2 GB.
   `xcrun simctl shutdown all` levou a memoria livre de ~90 MB para ~2,5 GB. **Sempre desligar
   o simulador ao terminar de usar.**
2. **Arquivos "dataless"**: `ls -lO` mostra `compressed,dataless` (flag `SF_DATALESS`, 0x40000000
   em `st_flags`). Contagem no dia: 1006 de 1013 arquivos do `dart-sdk`, todo o `flutter_web_sdk`,
   4137 de 4162 pacotes do Flutter e ate 309 de 449 arquivos do proprio projeto. `du` mostra 0 B.
   O snapshot do analisador tem 76 MB: o `dart analyze` fica parado esperando o download.

### Como conferir
```bash
df -h /System/Volumes/Data          # 98% cheio = macOS evicta sem parar
ls -lO $FLUTTER/bin/cache/dart-sdk/bin/snapshots/ | head    # "dataless"?
python3 -c "import os;print(sum(1 for d,_,f in os.walk('$FLUTTER/packages') for x in f if os.lstat(os.path.join(d,x)).st_flags & 0x40000000))"
```

### O que NAO resolve
Ler tudo com `cat` em paralelo materializa, mas com o disco cheio o macOS **re-evicta quase na
hora** (o projeto tinha 309 evictados antes e 339 depois de eu le-lo). Nao da para confiar em nada
que fique em `~/Documents`.

### O que resolve: verificar numa copia LOCAL, fora do iCloud
Copiar em paralelo (a leitura ja baixa) para `/tmp`: o `dart-sdk` (~550 MB), so os tres pacotes do
Flutter que o analisador le (`flutter`, `flutter_test`, `sky_engine`) e o codigo do projeto; reescrever
o `package_config.json` trocando o prefixo do SDK por sed. Resultado: **`dart analyze` do projeto
inteiro em 4 s** (antes: nunca terminava) e testes de dominio em 20 s.

```bash
cd $SRC && find . -type f -print0 | xargs -0 -P 24 -n 1 bash -c 'mkdir -p "$0/$(dirname "$1")" && cp -pP "$1" "$0/$1"' $DEST
```

### Teste puro sem os build hooks do forge2d
`dart test` num projeto que depende de `forge2d` tenta montar o hook nativo (compila C, 4 min, e um
build morto deixa `hook.dill`/snapshot de 0 byte -> `File truncated`). Para logica de dominio: um
**pacote-espelho** com so `test` e `meta`, com `lib/domain`, `lib/application` e `test` por link
simbolico. Roda sem Flutter e sem hooks. `dart test` num pacote sem hooks nao compila nada nativo.

### Sintomas que enganam
- exit **247** = SIGKILL (256-9) - o comando foi morto de fora, nao falhou;
- carga 28 numa maquina de 10 nucleos logo depois de matar processos e residuo, nao e trabalho ativo;
- `flutter doctor` reclamando de Rosetta e ruido: `impellerc` e arm64 e roda (`file` confirma).

### Recomendacao ao dono (nao apliquei)
Mover o SDK (`~/Documents/Repos/Flutter`) e os repositorios para fora de `~/Documents`
(por exemplo `~/dev`), ou liberar disco. Mexer em configuracao do iCloud e decisao dele.

### Espaco livre nao cura o que ja foi evictado (medido em 2026-09-26)
Com **16 GiB livres** (disco em 43%, contra os 4,3 GB da sessao anterior), `dart analyze lib test`
continuou **parado com 0,04 s de CPU em cinco minutos** - bloqueado em I/O, nao trabalhando.
Liberar espaco impede novas evicoes; nao traz de volta o que ja esta `dataless`. Ate alguem ler
cada arquivo (e o download acontecer), o SDK continua sendo baixado sob demanda a cada leitura.

**Consequencia pratica:** neste projeto o CI continua sendo o unico compilador, mesmo com disco
sobrando. Quem precisa de analise local tem de tirar o SDK de `~/Documents` - ver a secao de
correcao acima.

## Relacionado
- [[impellerc-morto-por-memoria-trava-build-flutter]]
- [[dart-analyze-parado-sem-cpu]]
- [[teste-de-fisica-no-navegador-com-teston-browser]]
