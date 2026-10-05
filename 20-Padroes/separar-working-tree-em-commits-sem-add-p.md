---
tipo: padrao
titulo: Separar um working tree misturado em commits sem git add -p
projeto: [todos]
stack: [git]
tags: [tipo/padrao, stack/git]
palavras-chave: [git, commit, add -p, interativo, hunk, separar commits, update-index, hash-object, cacheinfo, versao intermediaria, working tree misturado]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Separar um working tree misturado em commits sem git add -p
## Resumo
Quando o mesmo arquivo tem trechos de trabalhos diferentes e nao da para usar `git add -p` (agente, sem TTY), grave a versao intermediaria do arquivo direto no indice.

## Contexto
Usado no [[BTech.NFe.Api]] em 2026-09-11: o working tree tinha tres trabalhos (checklist de
seguranca pendente, banco em branco, correcao do importador) e `CLAUDE.md`, `Startup.cs`,
`README.md` etc. tinham trechos de mais de um. Um hunk so do `CLAUDE.md` misturava os tres —
`git apply --cached` com patch recortado nao resolveria.

## Detalhe
1. Script (Python) gera, para cada commit, o **conteudo completo** do arquivo naquela etapa:
   partindo do arquivo final e revertendo trechos, ou do `git show HEAD:<arq>` e aplicando trechos.
   Toda troca com `assert texto.count(antigo) == 1` — falha alto em vez de gerar versao errada.
2. Estagiar a versao sem tocar no working tree:
   ```bash
   estagiar() { local dir=$1; shift; for p in "$@"; do
     mode=$(git ls-files -s -- "$p" | awk '{print $1}')
     sha=$(git hash-object -w --path="$p" "$dir/$p")
     git update-index --cacheinfo "${mode:-100644},$sha,$p"; done; }
   ```
   `--path` aplica os filtros/atributos do caminho real.
3. Commit; repetir; o ultimo commit e `git add -A` (o working tree final).
4. Conferir: `git status` limpo, e o `--stat` do primeiro commit igual ao `git diff --stat`
   original daquele trabalho (foi assim que se provou que o trabalho pendente do Jonas saiu inteiro).

## Relacionado
- [[BTech.NFe.Api]]
- [[branch-mergeada-por-squash-e-apagada-no-remoto]]
