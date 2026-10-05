---
tipo: projeto
titulo: Cerebro
projeto: [Cerebro]
stack: [python, obsidian, claude-code, codex]
tags: [tipo/projeto, cerebro/sistema, stack/python]
palavras-chave: [cerebro, segundo cerebro, obsidian, vault, hooks, memoria, automacao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Cerebro

## Resumo
Sistema de memoria permanente que alimenta e consulta o vault Obsidian automaticamente em todo projeto.

## Contexto
Vault em `~/dev/Repos/Brain.Obsidian`. Motor em `~/.claude/cerebro/`. Sem dependencias externas:
usa apenas `/usr/bin/python3` (3.9) do sistema.

## Detalhe

### Como rodar / operar
```bash
cerebro doctor        # diagnostico
cerebro index         # reconstruir cache + 00-Indice
cerebro recall "x"    # consultar
cerebro pendencias    # fila deixada pelo Codex
```

### Estrutura
- `cerebro.py` — motor unico com subcomandos (`index`, `recall`, `nota`, `fila-add`, `doctor`,
  `hook-session-start`, `hook-user-prompt`, `hook-post-tool`, `hook-stop`, `hook-session-end`).
- `config.json` — vault, limiares de busca, gatilhos de captura, liga/desliga.
- `cache/indice.json` — indice invertido leve, incremental por mtime.
- `state/sessao-<id>.json` — dedupe de injecao e guarda anti-loop do Stop.
- `state/fila.jsonl` — pendencias vindas do Codex.

### Como testar um hook sem abrir sessao
```bash
echo '{"session_id":"t","cwd":"'$PWD'","prompt":"assunto"}' \
  | /usr/bin/python3 ~/.claude/cerebro/cerebro.py hook-user-prompt
```

### Convencoes
- Notas em portugues, nomes de arquivo sem acento.
- Frontmatter do [[Protocolo-Cerebro]] obrigatorio; `palavras-chave` e o campo que mais pesa na busca.

## Relacionado
- [[Protocolo-Cerebro]]
- [[Como-Funciona]]
- [[captura-via-stop-hook-em-vez-de-digest]]
