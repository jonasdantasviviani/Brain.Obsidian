---
tipo: sessao
titulo: Montagem do Cerebro (segundo cerebro automatico)
projeto: [Cerebro]
stack: [python, obsidian, claude-code, codex]
tags: [tipo/sessao, cerebro/sistema, stack/python]
palavras-chave: [cerebro, segundo cerebro, obsidian, hooks, memoria, automacao, recall, captura]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Montagem do Cerebro (segundo cerebro automatico)

## Resumo
Construido o sistema que faz Claude Code e Codex lerem e alimentarem o vault Obsidian sozinhos, em qualquer projeto.

## Contexto
Jonas tinha criado o cofre `~/Documents/Cerebro` vazio e queria que virasse memoria permanente,
alimentada automaticamente por qualquer agente, sem nunca precisar pedir.

## Detalhe

### O que foi construido

| Peca | Caminho |
| --- | --- |
| Motor (recall, indice, hooks) | `~/.claude/cerebro/cerebro.py` |
| Ajustes | `~/.claude/cerebro/config.json` |
| Hooks globais | `~/.claude/settings.json` |
| Regras para todo projeto | `~/.claude/CLAUDE.md` |
| Regras do Codex | `~/.codex/AGENTS.md` + `~/.codex/cerebro-notify.py` |
| Atalho de terminal | `~/.local/bin/cerebro` |
| Cores | `~/Documents/Cerebro/.obsidian/snippets/cerebro.css` |

### Hooks ligados

- `SessionStart` — injeta estatisticas, nota do projeto atual, preferencias e pendencias do Codex.
- `UserPromptSubmit` — busca por palavras-chave no vault e injeta as notas relacionadas.
- `PostToolUse` (Write/Edit/MultiEdit/NotebookEdit) — se a escrita foi no vault, marca a sessao
  como registrada e reindexa.
- `Stop` — analisa o transcript; se houve entrega real, bloqueia uma vez com o protocolo completo
  de registro. Guardas: `stop_hook_active` e contador `tentativas` no arquivo de estado.
- `SessionEnd` — regenera `00-Indice/*.md`.

### Decisoes de implementacao

- Busca sem dependencia externa: tokenizacao com remocao de acentos + stopwords PT/EN,
  cache incremental por `mtime`+tamanho em `~/.claude/cerebro/cache/indice.json`.
- Score: campos fortes (titulo, tags, projeto, stack, palavras-chave) x5, corpo x1 ate 6 acertos,
  bonus +7 quando a nota e do projeto atual, notas de sessao pesam 0.6 para nao afogar o resto.
- Deduplicacao por sessao: uma nota ja injetada nao volta no mesmo `session_id`.
- Python 3.9 do sistema (`/usr/bin/python3`), sem pip, para o hook nunca quebrar por ambiente.

### Comandos que funcionaram

```bash
cerebro index                     # reconstroi cache e indices
cerebro recall "assunto"          # o que ja sei sobre isso
cerebro doctor                    # diagnostico
cerebro nota padrao "Titulo"      # nota nova ja com frontmatter
cerebro pendencias                # fila deixada pelo Codex
```

Teste de hook fora da sessao:

```bash
echo '{"session_id":"t","cwd":"'$PWD'","prompt":"assunto"}' \
  | /usr/bin/python3 ~/.claude/cerebro/cerebro.py hook-user-prompt
```

## Carga inicial do vault (mesma sessao, segunda parte)

Varredura de todos os repositorios de `~/Documents/Repos` + leitura dos transcripts antigos do
Claude Code em `~/.claude/projects/`, convertidos em 31 notas.

### Fontes que renderam mais conhecimento
| Fonte | O que rendeu |
| --- | --- |
| `CLAUDE.md` de cada repo | stack real, convencoes, armadilhas ja documentadas |
| `Heavy/.claude/skills/` e `BTech/.claude/skills/` | os padroes obrigatorios de arquitetura |
| Docs `00-28` do Heavy | decisoes de produto e de arquitetura com o *porque* |
| `git log --oneline` | mapa do que foi entregue e em que ordem |
| Transcripts JSONL antigos | o que o Jonas pediu, com as palavras dele |

### Descobertas que so apareceram cruzando fontes
- `Repos/Pessoal/Transporte` (transcript de agosto) e o **Heavy** de hoje — o projeto foi renomeado.
- `Sites/btech-nfe-web` e `BTech/BTech.Web` sao o **mesmo produto**: o `package.json` do segundo
  ainda se chama `btech-nfe-web`, e o primeiro guarda o historico detalhado das telas.
- A sequencia de commits do Heavy (dominio → sql → teste → aplicacao → infra → webapi) prova na
  pratica a regra de dependencia da [[arquitetura-dotnet-em-camadas]].

### Verificacao final
- 45 notas indexadas, 273 conexoes `[[wiki]]`, **zero links quebrados**
- Recall testado em 5 contextos diferentes: cada projeto recebe suas proprias armadilhas
  e padroes antes de qualquer codigo ser escrito

## Relacionado
- [[Protocolo-Cerebro]]
- [[Como-Funciona]]
- [[cerebro-sempre-automatico]]
- [[arquitetura-dotnet-em-camadas]]
