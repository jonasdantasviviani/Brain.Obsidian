---
tipo: armadilha
titulo: Repositorio oscilando entre CRLF e LF sem .gitattributes
projeto: [btech-nfe-web]
stack: [git]
tags: [tipo/armadilha, stack/git]
palavras-chave: [crlf, lf, quebra de linha, gitattributes, diff, ruido, windows, mac, line ending, autocrlf]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Repositorio oscilando entre CRLF e LF sem .gitattributes
## Resumo
Sem `.gitattributes`, o mesmo arquivo alterna entre CRLF e LF conforme a maquina e gera diffs gigantes sem uma linha de conteudo mudada.

## Contexto
Encontrado em 07/09/2026 no [[btech-nfe-web]]: 16 arquivos com **1814 insercoes e 1814
delecoes** — numeros identicos, sinal classico.

## Detalhe

### Como identificar em dez segundos
```bash
git diff --stat --ignore-all-space   # se esvazia, e so espaco/quebra de linha
file src/lib/api.ts                  # "with CRLF line terminators"
```
Insercoes == delecoes e o primeiro indicio. O `--ignore-all-space` confirma.

### Por que acontece
O repositorio foi editado em maquinas com convencoes diferentes (ou por ferramentas
diferentes na mesma maquina) sem nenhuma normalizacao no git. Cada gravacao reescreve o
arquivo inteiro na convencao local.

### O custo real
- `git blame` inutil: toda linha aponta para o commit de normalizacao
- Merge e rebase com conflito em arquivo que ninguem tocou
- Code review afogado — 1814 linhas de ruido escondem a mudanca de verdade
- O historico incha

### A correcao
`.gitattributes` na raiz:
```gitattributes
* text=auto
*.sh text eol=lf
*.ps1 text eol=crlf
*.png binary
```
Depois, um commit unico de normalizacao:
```bash
git add --renormalize .
git commit -m "chore: normaliza quebras de linha via .gitattributes"
```
Faca isso **sozinho num commit**, nunca junto de mudanca real.

### O que foi feito nesta sessao
As mudancas de CRLF foram commitadas **separadas** das de seguranca, exatamente para nao
misturar ruido com conteudo. O `.gitattributes` ainda **nao** foi criado — fica como
proximo passo, senao o problema volta na proxima maquina.

## Relacionado
- [[btech-nfe-web]]
- [[2026-09-07-correcao-das-pendencias-de-seguranca]]
