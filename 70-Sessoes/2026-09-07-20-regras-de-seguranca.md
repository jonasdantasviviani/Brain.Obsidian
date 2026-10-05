---
tipo: sessao
titulo: As 20 regras de seguranca e a auditoria automatica
projeto: [Cerebro, BTech.NFe.Api, BTech.Web, Heavy, ICook]
stack: [python, seguranca]
tags: [tipo/sessao, stack/python, seguranca]
palavras-chave: [seguranca, 20 regras, auditoria, checklist, vulnerabilidade, owasp, mass assignment, rls, dependabot, headers]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# As 20 regras de seguranca e a auditoria automatica
## Resumo
As 20 regras viraram notas no Cerebro e uma auditoria que roda sozinha no inicio de cada sessao.

## Contexto
07/09/2026. Jonas listou 20 itens de seguranca que devem valer para todo projeto e ser validados
a cada sessao.

## Detalhe

### O que foi construido
| Peca | Onde |
| --- | --- |
| 20 notas, uma por regra | `80-Seguranca/01-…` a `20-…` |
| Checklist consolidado, regerado a cada `index` | `80-Seguranca/Checklist-Seguranca.md` |
| Auditor | `~/.claude/cerebro/auditar.py` |
| Injecao automatica | hook `SessionStart` do [[Cerebro]] |
| Comando manual | `cerebro seguranca <caminho>` |

### Resultado da primeira auditoria (6 projetos)

| Projeto | ok | corrigir | revisar |
| --- | --- | --- | --- |
| [[Heavy]] | 11 | 6 | 1 |
| [[BTech.NFe.Api]] | 10 | 3 | 5 |
| [[BTech.Web]] | 4 | 4 | 2 |
| [[btech-nfe-web]] | 4 | 4 | 2 |
| [[ICook]] | 4 | 8 | 1 |

### Achados que valem acao
1. **Regra 20 falta em TODOS** — nenhum projeto tem scan de dependencia. E a mais barata:
   6 linhas de `dependabot.yml`. Comecar por aqui.
2. **Regra 12 falta em todos** — nenhum formulario tem protecao contra bot.
3. **[[BTech.NFe.Api]]: mass assignment real** — controllers recebem entidades EF scaffolded
   direto (`[FromBody] CabNota`), expondo todas as colunas da tabela na entrada **e** na saida.
   Ver [[08-bloquear-mass-assignment]] e [[17-trim-nas-respostas-de-api]].
4. **[[Heavy]]: coluna `senha_hash` existe, algoritmo de hash nao** — quando o login for
   implementado, usar Argon2id/BCrypt desde a primeira linha.
5. **[[Heavy]]: sem `UseHttpsRedirection`/`UseHsts` e sem security headers.**
6. **[[BTech.Web]]: token de sessao em `localStorage`** — preferir cookie `HttpOnly`.

### O que ja esta certo e serve de referencia
- [[Heavy]]: RLS com `set_config` LOCAL, DTOs em tudo, filtro por dono em tres camadas
- [[BTech.NFe.Api]]: BCrypt com teste, rate limit no login com teste, HTTPS+HSTS, security
  headers, upload validado

### Calibragem
Tres rodadas de falso positivo antes de confiar no auditor —
detalhe em [[auditoria-por-evidencia-em-vez-de-sast]].

## Relacionado
- [[Checklist-Seguranca]]
- [[auditoria-por-evidencia-em-vez-de-sast]]
- [[2026-09-07-cerebro-montagem-do-sistema]]
