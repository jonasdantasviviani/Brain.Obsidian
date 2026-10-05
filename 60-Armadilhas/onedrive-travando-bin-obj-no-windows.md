---
tipo: armadilha
titulo: OneDrive trava bin/obj e quebra o build .NET no Windows
projeto: [BTech.NFe.Api]
stack: [dotnet, windows]
tags: [tipo/armadilha, stack/dotnet]
palavras-chave: [onedrive, windows, build, lock, bin, obj, dotnet, erro intermitente, file lock]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# OneDrive trava bin/obj e quebra o build .NET no Windows
## Resumo
Repositorio dentro de pasta sincronizada pelo OneDrive causa erro de file lock intermitente no build.

## Contexto
Registrado no CLAUDE.md da [[BTech.NFe.Api]].

## Detalhe

### Sintoma
Build falha **intermitentemente** com erro de arquivo travado em `bin/` ou `obj/`.
Rodar de novo as vezes funciona — o que faz parecer flakiness do MSBuild.

### Causa
O OneDrive sincroniza `bin/` e `obj/` enquanto o MSBuild escreve neles, e trava os arquivos no
meio do build.

### Solucao
**Mover o clone para fora da pasta do OneDrive.**

### Correlato
Se o build da solucao inteira ficar instavel por outros motivos, compile os projetos tocados
individualmente em vez de `dotnet build BTech.NFe.slnx`.

## Relacionado
- [[BTech.NFe.Api]]
- [[dotnet-10]]
