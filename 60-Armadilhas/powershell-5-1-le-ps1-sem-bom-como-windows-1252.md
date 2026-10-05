---
tipo: armadilha
titulo: Windows PowerShell 5.1 le .ps1 sem BOM como Windows-1252
projeto: [BTech.NFe.Api, BTech.Web, todos]
stack: [powershell, windows]
tags: [tipo/armadilha, stack/powershell, stack/windows]
palavras-chave: [powershell, ps1, bom, utf-8, windows-1252, cp1252, acento, mojibake, travessao, powershell 5.1, pwsh, executionpolicy, gitattributes, crlf]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Windows PowerShell 5.1 le .ps1 sem BOM como Windows-1252
## Resumo
O PowerShell que vem no Windows (5.1) le script UTF-8 **sem BOM** como Windows-1252: "não" vira `nÃ£o`, "—" vira `â€”`. O pwsh 7 (Mac/Linux) le UTF-8 normalmente, entao o problema so aparece na maquina Windows.

## Contexto
2026-09-11: scripts `.ps1` da [[BTech.NFe.Api]] e do [[BTech.Web]] escritos no Mac com acentos e
travessao. Simulado com `pwsh` + parser (`[Parser]::ParseInput` sobre o texto decodificado em cp1252):
nesses arquivos nao houve erro de parse, so mensagens ilegiveis — mas `”` (0x94 em cp1252, que vem
do UTF-8 de "—") e aspas para o PowerShell, entao em outro contexto quebra a sintaxe.

## Detalhe
- Salvar `.ps1` em **UTF-8 com BOM** (bytes `EF BB BF`). Script de verificacao em Python:
  prefixa o BOM se faltar.
- `.gitattributes`: `*.sh text eol=lf` (bash quebra com CRLF, e o Windows com `core.autocrlf=true`
  poria CRLF) e `*.ps1 text eol=crlf`.
- Documentar o bloqueio de execucao: `powershell -ExecutionPolicy Bypass -File .\script.ps1` ou
  `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`.
- Compatibilidade 5.1: sem `&&`/`||` entre comandos, sem ternario `? :`; `Invoke-WebRequest`
  precisa de `-UseBasicParsing` e lanca excecao em status != 2xx.
- Checar sintaxe no Mac: `pwsh` esta instalado (`/usr/local/bin/pwsh`).

## Relacionado
- [[repositorio-oscilando-entre-crlf-e-lf]]
- [[failed-to-fetch-no-front-btech]]
