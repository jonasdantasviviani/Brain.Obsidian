---
tipo: sessao
titulo: Manual de setup local para Windows e macOS na BTech.NFe.Api
projeto: [BTech.NFe.Api]
stack: [dotnet, docker, windows, macos]
tags: [tipo/sessao, stack/dotnet, empresa/btech]
palavras-chave: [setup, instalacao, manual, windows, mac, macos, docker, script, powershell, bash, quickstart, bak]
origem: claude-code
criado: 2026-09-02
atualizado: 2026-09-07
confianca: alta
---

# Manual de setup local para Windows e macOS na BTech.NFe.Api
## Resumo
Criacao do `INSTALLATION.md` e dos scripts de setup por sistema operacional, com validacao de `.bak` real.

## Contexto
02/09/2026, em `Repos/BTech/BTech.NFe.Api`. 105 comandos bash, 14 arquivos tocados.
Pedido: *"Ajuste o que esta faltando para os projetos, e deixe um manual bem facil para rodar
local em maquina Windows e Mac"* seguido de *"Pode rodar, **caso nao consiga instale o que falta
ate conseguir**"*.

## Detalhe

### O que saiu
Commit `58a6c46 Fix local setup docs/scripts for Windows+macOS, add a working quick-start guide`.

- `INSTALLATION.md` — guia rapido por SO, incluindo restaurar um `.bak` para teste
- `scripts/` — automacao de setup: `.ps1` para Windows, `.sh` para macOS/Linux
- `backups/` montado no container do SQL Server
- Endpoints de apoio: `POST /api/admin/database/validate-local-backup` e
  `GET /api/admin/database/schema-status`

### Armadilha descoberta e documentada
[[onedrive-travando-bin-obj-no-windows]] — no Windows, manter o clone fora de pasta sincronizada
pelo OneDrive.

### Padrao do Jonas
"Pode rodar, caso nao consiga instale o que falta ate conseguir" — ele espera que o agente
**resolva o ambiente**, nao que pare pedindo autorizacao a cada dependencia faltando.

## Relacionado
- [[BTech.NFe.Api]]
- [[onedrive-travando-bin-obj-no-windows]]
- [[docker-compose-nos-projetos]]
