---
tipo: armadilha
titulo: Preview do Claude app nao consegue rodar servidor de dentro de ~/Documents
projeto: [todos]
stack: [macos, claude-code]
tags: [tipo/armadilha, stack/macos]
palavras-chave: [preview_start, launch.json, operation not permitted, tcc, privacidade, documents, getcwd, dev server, navegador embutido]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Preview do Claude app nao consegue rodar servidor de dentro de ~/Documents
## Resumo
`preview_start` com `.claude/launch.json` falha com `Operation not permitted` (e `getcwd: cannot access parent directories`) quando o projeto esta em `~/Documents`: a protecao de privacidade do macOS (TCC) nao libera a pasta para o processo do preview.

## Contexto
2026-09-11, subindo o [[BTech.Web]] para validar no navegador embutido.

## Detalhe
Contorno: subir o servidor pelo Bash (que tem acesso) em segundo plano e abrir o navegador
embutido so na URL:
```bash
./scripts/dev-local.sh > dev.log 2>&1   # run_in_background
```
depois `navigate` para `http://localhost:3000`. Definitivo: repos fora de `~/Documents` — o que
tambem resolve [[icloud-evicta-node-modules-e-tsc-trava]].

## Atualizacao 2026-09-16 — sem ferramenta de navegador nesta sessao (CLI/extensao VSCode)
Nesta sessao (Claude Code como extensao VSCode, sem `preview_start`/`navigate`) o sintoma foi
diferente mas a causa e capaz de ser a mesma familia (sandbox de rede/TCC em `~/Documents`):
- `npm run dev > log 2>&1 &` (subshell entre parenteses, backgrounded manualmente) sobe o processo
  mas ele fica preso em 0% CPU pra sempre e o log nunca recebe nem a primeira linha ("▲ Next.js
  ...") — parece o mesmo sintoma do node_modules evictado pelo iCloud
  ([[icloud-evicta-node-modules-e-tsc-trava]]), mas rodar `brctl download node_modules` e
  reiniciar **nao resolveu** desta vez.
- Corrigido invocando o Bash tool diretamente com o comando de dev (sem `&`/subshell manual,
  deixando o proprio harness mover pra background com `run_in_background`): `npx next dev` assim
  respondeu em 130ms com "✓ Ready".
- Mesmo com o servidor de pe, `curl http://localhost:3000` **trava sem erro** (nao da connection
  refused, so nunca retorna) — network loopback do Bash tool parece bloqueado/sandboxado nesta
  configuracao. Sem ferramenta de preview/navegador disponivel nesta sessao, nao ha como validar
  visualmente uma mudanca de UI aqui: o maximo verificavel e build/typecheck/lint limpos +
  testes de contrato batendo nos endpoints reais do backend. Avisar o usuario disso explicitamente
  em vez de alegar "testado no navegador".

## Relacionado
- [[icloud-evicta-node-modules-e-tsc-trava]]
- [[BTech.Web]]
