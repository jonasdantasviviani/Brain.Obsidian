---
tipo: armadilha
titulo: iCloud tira node_modules do disco e tsc/npm travam com CPU zero
projeto: [BTech.Web, todos]
stack: [macos, icloud, node, typescript, next]
tags: [tipo/armadilha, stack/node, stack/macos]
palavras-chave: [icloud, dataless, otimizar armazenamento, optimize mac storage, documents, node_modules, tsc trava, npx trava, eslint trava, cpu zero, fileprovider, brctl]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# iCloud tira node_modules do disco e tsc/npm travam com CPU zero
## Resumo
Os repos ficam em `~/Documents`, sincronizado pelo iCloud; com "Otimizar armazenamento" o macOS evicta arquivos pouco usados e cada leitura espera download — `tsc` fica parado com 0% de CPU.

## Contexto
Em 2026-09-11, `npx tsc --noEmit` no [[BTech.Web]] rodou 17 min sem terminar, com 0% de CPU. O
`node_modules` tinha **39.995 arquivos `dataless`** (inclusive `typescript/lib/*.d.ts`); o `src/`
estava inteiro. O download sob demanda andava ~16 arquivos em minutos.

## Detalhe
Diagnostico:
```bash
ls -lO node_modules/typescript/lib/lib.dom.d.ts       # "compressed,dataless" = evictado
find node_modules -flags +dataless | wc -l
```
Solucoes (escolha do Jonas — mexem no disco/rede):
- Reinstalar local: `rm -rf node_modules && npm ci` (arquivos novos, ainda no iCloud — podem ser
  evictados de novo).
- Forcar download: `brctl download node_modules` e esperar.
- Definitivo: tirar os repos de `~/Documents` (ex.: `~/Code`) ou desligar "Otimizar armazenamento"
  do Mac. Pasta de dependencia/build (`node_modules`, `bin`, `obj`, `.next`) nao deveria ir
  para nuvem — mesma familia de [[onedrive-travando-bin-obj-no-windows]].

Sintoma que confunde: nao ha erro, so demora; um processo "vivo" com 0% de CPU e estado `S` e a pista.

## Relacionado
- [[BTech.Web]]
- [[onedrive-travando-bin-obj-no-windows]]
