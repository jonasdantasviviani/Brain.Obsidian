---
tipo: stack
titulo: Playwright para testes E2E
projeto: [BTech.Web]
stack: [playwright, typescript]
tags: [tipo/stack, stack/playwright]
palavras-chave: [playwright, e2e, teste, end to end, auth, storage state, ui mode]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Playwright para testes E2E
## Resumo
Testes end-to-end do front-end BTech, com estado de autenticacao reaproveitado entre specs.

## Detalhe

```bash
npm run test:e2e       # headless
npm run test:e2e:ui    # modo UI, para depurar
```

### Estrutura
- `playwright.config.ts` na raiz
- specs em `tests/e2e/`
- `playwright/.auth/` guarda o **storage state** da sessao autenticada, para os testes nao
  refazerem login a cada spec

### Onde encaixa na piramide
No back-end .NET a divisao e outra: quatro projetos separados
(Unit · Contract · Functional · Integration). Ver [[arquitetura-dotnet-em-camadas]].

## Relacionado
- [[BTech.Web]]
- [[next-16-react-19]]
