---
tipo: armadilha
titulo: Mudar o vault de lugar exige atualizar 5 arquivos de configuração
projeto: [Cerebro]
stack: [claude-code, python, obsidian]
tags: [tipo/armadilha, stack/claude-code]
palavras-chave: [vault, caminho, mudar de pasta, settings.json, autoMemoryDirectory, config.json, cerebro.py, AGENTS.md]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

## Resumo
Em 05/10/2026 o vault saiu de `~/Documents/Cerebro` para `~/dev/Repos/Brain.Obsidian`; o caminho antigo estava gravado em 5 lugares, e o hook falha em silêncio se um ficar para trás.

## Contexto
Movido para fora do iCloud (ver [[git-status-trava-com-arquivos-dataless-do-icloud]]). O vault agora é um repositório Git (`jonasdantasviviani/Brain.Obsidian`).

## Detalhe
Onde o caminho vive (todos atualizados, com backup em `~/.claude/cerebro/backup-caminho-*`):
- `~/.claude/CLAUDE.md` (3 menções) e `~/.codex/AGENTS.md`
- `~/.claude/settings.json`: `additionalDirectories`, regras `Read/Edit/Write(.../**)` e `autoMemoryDirectory`
- `~/.claude/cerebro/config.json` (`vault`) e o padrão em `~/.claude/cerebro/cerebro.py`
Conferir depois: `python3 ~/.claude/cerebro/cerebro.py recall "teste"` e `... index`. Sessões já abertas continuam com o caminho antigo no contexto; vale reiniciar a sessão.

## Relacionado
[[Cerebro]] [[Protocolo-Cerebro]]
