---
tipo: armadilha
titulo: Agentes em paralelo batem no limite de uso e perdem tudo o que nao commitaram
projeto: [RagdollGames]
stack: [claude-code, git]
tags: [tipo/armadilha, ferramenta/claude-code, git, worktree, paralelismo]
palavras-chave: [subagente, agente em paralelo, worktree, limite de uso, rate limit, 429, session limit, commit frequente, trabalho perdido]
origem: claude-code
criado: 2026-09-13
atualizado: 2026-09-13
confianca: alta
---

# Agentes paralelos sem commit perdem o trabalho no limite de uso

## Resumo
Cinco subagentes em worktrees isolados, lancados juntos a pedido do Jonas ("faca todos em paralelo"),
consumiram o limite de uso da sessao. Tres morreram com erro 429 e dois foram parados; nenhum tinha
commitado. Os worktrees foram limpos e nenhum branch sobrou: todo o trabalho se perdeu.

## Causa
- Varios agentes Opus simultaneos gastam o limite muito mais rapido que uma sessao so.
- Worktree de agente e descartado quando o agente termina sem commit: mudanca nao commitada nao sobrevive.

## Como evitar
- No prompt de cada agente: **commits pequenos e frequentes**, um por sub-item que passou nos testes.
- Dividir a tarefa de cada agente em entregas commitaveis, com ordem sugerida.
- Antes de relancar, conferir `git worktree list` e `git branch -a` para ver o que sobrou.

## Relacionado
- [[trabalhar-em-paralelo]]
- [[2026-09-12-counter-ragdoll-modo-bomba]]
