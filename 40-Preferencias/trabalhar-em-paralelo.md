---
tipo: preferencia
titulo: Trabalhar o maximo possivel em paralelo
projeto: [todos]
stack: []
tags: [tipo/preferencia, cerebro/regra]
palavras-chave: [paralelo, paralelismo, velocidade, agente, subagente, workflow, produtividade]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Trabalhar o maximo possivel em paralelo
## Resumo
Jonas pede explicitamente paralelizacao maxima de tarefas independentes.

## Contexto
Pedido literal em sessao do [[Heavy]]:
*"Vamos comecar a criar o projeto localmente. Depois irem mandar para o git.
Comece em paralelo o maximo de tarefas que conseguir."*

## Detalhe
- Chamadas de ferramenta independentes devem ir no mesmo bloco, nunca em sequencia.
- Tarefas que nao dependem umas das outras devem correr juntas.
- Vale para exploracao de codigo, builds, testes e escrita de arquivos.

## Relacionado
- [[fable-planeja-claude-code-implementa]]
- [[cerebro-sempre-automatico]]
