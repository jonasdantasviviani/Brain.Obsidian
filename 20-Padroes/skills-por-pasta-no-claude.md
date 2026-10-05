---
tipo: padrao
titulo: Skills obrigatorias por pasta em .claude/skills
projeto: [Heavy, ICook]
stack: [claude-code]
tags: [tipo/padrao, stack/claude-code, cerebro/padrao-obrigatorio]
palavras-chave: [skill, claude, claude code, arquitetura, design system, convencao, agente, padrao por pasta]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Skills obrigatorias por pasta em .claude/skills
## Resumo
Cada pasta do monorepo tem uma skill que deve ser carregada **antes** de criar qualquer arquivo nela.

## Contexto
Padrao do [[Heavy]], com `.claude/skills/` versionado no repositorio. O [[ICook]] usa a mesma
ideia com `docs/ai/` (constituicao para IA) separado de `.planning/` (planejamento de produto).

## Detalhe

### Mapa do Heavy
| Vai mexer em | Skill |
| --- | --- |
| `backend/` | `backend-architecture` (+ `references/projeto-heavyops.md`) |
| `painel/` arquitetura, camadas, hooks, chamadas de API | `frontend-architecture` |
| `painel/` cor, fonte, espacamento, componente visual | `design-system-frontend` |
| `app/` arquitetura, Bloc/Cubit, repositorios, `get_it` | `mobile-architecture` |
| `app/` cor, fonte, espacamento, tela | `design-system-mobile` |
| `infra/` | nenhuma; seguir doc 22 e `infra/README.md` |

### Regras
- A skill **decide em qual camada o arquivo mora** — nao improvisar estrutura.
- As duas skills de design system compartilham **os mesmos nomes de token**.
  Valor cru de cor, fonte, espacamento ou raio no codigo e erro nas duas.
- A skill e generica e reutilizavel entre produtos; o que e especifico de um produto vive em
  `references/`, nunca na skill.

### Separacao de documentacao que funciona (ICook)
| Camada | Local | Responsabilidade |
| --- | --- | --- |
| Planejamento de produto | `.planning/` | o que construir, em que ordem, por que |
| Constituicao para IA | `docs/ai/` | como pensar neste codigo — **regras, nao descricoes** |
| Decisoes pontuais | `docs/decisions/` | ADRs |

**Nunca duplicar conteudo entre as camadas.**

## Relacionado
- [[Heavy]]
- [[ICook]]
- [[Cerebro]]
