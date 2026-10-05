---
tipo: preferencia
titulo: Fable planeja, Claude Code implementa
projeto: [ICook, todos]
stack: [claude-code]
tags: [tipo/preferencia, cerebro/regra, stack/claude-code]
palavras-chave: [fable, claude code, agente, divisao, papel, planejamento, implementacao, rfc, arquitetura]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Fable planeja, Claude Code implementa
## Resumo
Agente de planejamento e agente de implementacao tem papeis separados; nenhum faz o papel do outro.

## Contexto
Formalizado no CLAUDE.md do [[ICook]], mas e a forma como Jonas trabalha em geral.

## Detalhe

| Agente | Faz |
| --- | --- |
| **Fable** | entender problemas, pesquisar, desenhar arquitetura, decompor tarefas, revisar solucoes, criar RFCs, validar trade-offs |
| **Claude Code** | implementacao, testes, refatoracao localizada, documentacao, execucao do plano |

### A regra que importa
> Se uma tarefa de implementacao revela uma **decisao arquitetural nao tomada**, pare e sinalize.
> Nao decida sozinho uma questao de arquitetura em pleno codigo.

### Fluxo obrigatorio de trabalho (ICook, generalizavel)
1. **Investigar** — ler os documentos relevantes e o codigo existente antes de propor solucao
2. **Planejar** — para qualquer mudanca que toque mais de um arquivo ou introduza padrao novo
3. **Implementar** — seguindo os padroes ja estabelecidos
4. **Revisar** — contra as regras de negocio e constraints
5. **Documentar** — ADR se houve decisao arquitetural nova

### Principio relacionado
> Codigo e a fonte de verdade sobre documentacao quando elas divergem.
> Se a doc contradiz o codigo real, **aponte a divergencia** em vez de assumir silenciosamente
> qual esta certo.

(No [[Heavy]] a regra e a inversa e explicita: o **doc** e a fonte da verdade. Confira em qual
projeto voce esta.)

## Relacionado
- [[ICook]]
- [[Heavy]]
- [[trabalhar-em-paralelo]]
