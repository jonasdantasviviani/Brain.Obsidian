---
tipo: sistema
titulo: Como Funciona
tags: [cerebro/sistema]
atualizado: 2026-09-07
---

# Como o Cerebro funciona

## Fluxo automatico

```
voce escreve um pedido em QUALQUER projeto
        |
        v
[hook UserPromptSubmit] -> cerebro.py recall -> injeta notas relacionadas no contexto
        |
        v
   Claude trabalha ja sabendo o que voce ja decidiu antes
        |
        v
[hook Stop] -> detecta que houve entrega -> obriga o registro no vault
        |
        v
[hook PostToolUse] -> reindexa a cada nota escrita
        |
        v
[hook SessionEnd] -> regenera 00-Indice/*.md
```

## Pecas

| Peca | Onde |
| --- | --- |
| Motor (recall, indice, hooks) | `~/.claude/cerebro/cerebro.py` |
| Ajustes | `~/.claude/cerebro/config.json` |
| Hooks | `~/.claude/settings.json` |
| Regras para todo projeto | `~/.claude/CLAUDE.md` |
| Regras para o Codex | `~/.codex/AGENTS.md` |
| Atalho de terminal | `~/.local/bin/cerebro` |
| Este vault | `~/dev/Repos/Brain.Obsidian` |

## Comandos uteis

```bash
cerebro recall "autenticacao jwt"   # o que ja sei sobre isso?
cerebro index                       # reconstruir indices
cerebro doctor                      # esta tudo de pe?
cerebro nota padrao "Retry HTTP"    # criar nota ja com frontmatter
cerebro pendencias                  # o que o Codex deixou para registrar
```

## Pastas

| Pasta | Cor | Conteudo |
| --- | --- | --- |
| `00-Indice` | cinza | painel e indices, gerados automaticamente |
| `10-Projetos` | azul | um arquivo por projeto: stack, como rodar, estrutura |
| `20-Padroes` | verde | solucoes reutilizaveis entre projetos |
| `30-Decisoes` | roxo | escolhas tecnicas e o que foi descartado |
| `40-Preferencias` | rosa | como voce gosta que as coisas sejam feitas |
| `50-Stack` | laranja | conhecimento por tecnologia |
| `60-Armadilhas` | vermelho | erros que ja custaram tempo |
| `70-Sessoes` | amarelo | diario bruto do que foi feito |
| `90-Sistema` | cinza | esta documentacao |
| `91-Memoria-Auto` | ciano | memoria nativa do Claude Code |

## Desligar temporariamente

Edite `~/.claude/cerebro/config.json`:
`"recall_automatico": false` desliga a injecao, `"captura_automatica": false` desliga a cobranca do registro.
