---
tipo: armadilha
titulo: Next.js 16 nao e o Next que o modelo conhece
projeto: [BTech.Web, btech-nfe-web]
stack: [next, react]
tags: [tipo/armadilha, stack/next, cerebro/critico]
palavras-chave: [next, nextjs, next 16, breaking change, api, convencao, documentacao, node_modules, treino, deprecation]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Next.js 16 nao e o Next que o modelo conhece
## Resumo
Next 16 tem breaking changes em APIs, convencoes e estrutura de arquivos em relacao ao conhecimento de treino dos modelos.

## Contexto
Aviso colocado explicitamente no topo do `AGENTS.md` de [[BTech.Web]] e [[btech-nfe-web]]:

> This version has breaking changes — APIs, conventions, and file structure may all differ from
> your training data.

## Detalhe

### A regra
**Leia o guia relevante em `node_modules/next/dist/docs/` antes de escrever codigo.**
Preste atencao nos avisos de deprecacao.

### Por que isso importa aqui
Escrever "de cabeca" um padrao de Next 13/14/15 gera codigo que compila e falha em runtime, ou
que usa API removida. Custa uma rodada inteira de debug para descobrir que a premissa estava
errada desde o inicio.

### Como o repo se protege
O aviso esta em `AGENTS.md`, que o `CLAUDE.md` importa com `@AGENTS.md` — entao carrega em toda
sessao, para qualquer agente.

## Relacionado
- [[next-16-react-19]]
- [[BTech.Web]]
