---
tipo: sessao
titulo: BTech testes e2e com API simulada (retomada)
projeto: BTech.Web
stack: [playwright, axe-core, next, node]
tags: [e2e, acessibilidade, mock, testes]
palavras-chave: [playwright, mock api, openapi, axe, a11y, e2e.yml]
origem: sessao claude code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: alta
---
# Retomada da frente e2e (PR #63, branch test/e2e-mock)

- Retomado apos limite de uso: commitado o trabalho sujo (empresa/home), corrigido comentario `//` que engolia o resto de uma linha em `mock-api/rotas.ts` (o mock nao subia).
- Mock em `tests/e2e/mock-api/` gera respostas do `contract/openapi.json` e reprova teste que saia do contrato.
- Novo: `acessibilidade.spec.ts` (axe, falha em serious/critical), `telas.spec.ts` (capturas), `e2e.yml` (manual, semanal, PR com rotulo `e2e`).
- Bugs de UI achados pelo axe e corrigidos: botoes so-icone sem nome (voltar/editar/excluir), checkboxes da DataTable, SelectTrigger sem nome (herda o label do campo), links so por cor, eixo Y do grafico quebrado (width 64 -> 80).
- Resultado: 136 e2e passando, vitest 143, tsc limpo.

## Armadilhas
- [[Playwright reuseExistingServer]]: servidor antigo na porta 3191 ficou vivo e os testes rodaram contra build velho; matar `next start`/mock antes de rebuild.
- recharts pula rotulos do eixo conforme a largura: nao assertar tick especifico; e `getByText` nao enxerga filhos de `role=img`.
- No macOS `sed -i` com `/` ou `|` no padrao quebra silenciosamente: usar python para edicoes.
