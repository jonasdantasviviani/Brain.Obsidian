---
tipo: sessao
titulo: BTech.Web — migracao das 47 ocorrencias de set-state-in-effect
projeto: [BTech.Web]
stack: [react, next, typescript]
tags: [tipo/sessao, stack/react, empresa/btech]
palavras-chave: [set-state-in-effect, useCarregar, useSyncExternalStore, refatoracao, lotes, lint, AuthContext, react compiler]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# BTech.Web — migracao das 47 ocorrencias de set-state-in-effect
## Resumo
47 → 0 em 41 arquivos, em 6 lotes (infra, admin, cadastros, pedidos/financeiro, fiscal, relatorios); regra de volta a error; lint 0 erros, tsc e next build ok. Nao commitado.

## Contexto
Tarefa sugerida no PR Web #11, que tinha rebaixado a regra para warn. Padrao em
[[carregar-dados-sem-setstate-em-efeito]].

## Detalhe
- Branch `refactor/carregamento-sem-setstate-em-efeito` (da main `4b3eacc`, que ja trazia TS 6.0.3).
- Validacao por lote com script que compara com o lint da main: erros, ocorrencias restantes,
  **avisos novos por arquivo** (pegou 5 avisos introduzidos na importacao, corrigidos) e tsc.
- Lint final: 0 erros, 27 avisos (a main tinha 28 fora da regra). `next build`: 47 paginas.
- UI sem login: branding do login com `?empresa=padrao` / slug inexistente ok, sem erro de
  hidratacao. Telas autenticadas dependem do Jonas fazer o login na tela (nao digito senha).
- Achado: emissao em lote simulada (virou tarefa sugerida).
- Descoberta tecnica: a regra rastreia funcao local que chama setState mesmo apos `await`.

## Relacionado
- [[BTech.Web]]
- [[carregar-dados-sem-setstate-em-efeito]]
