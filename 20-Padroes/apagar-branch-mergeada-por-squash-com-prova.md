---
tipo: padrao
titulo: "Apagar branch mergeada por squash só com prova (merge-tree contra o commit do PR)"
projeto: [todos]
stack: [git, github]
tags: [tipo/padrao, stack/git]
palavras-chave: [squash, merge-tree, apagar branch, branch mergeada, limpeza de branches, deixar so a main, git cherry, pr mergeado, worktree]
origem: claude-code
criado: 2026-09-28
atualizado: 2026-09-28
confianca: alta
---

# Apagar branch mergeada por squash só com prova

## Resumo
Depois de squash, a branch aparece "N commits à frente" para sempre. Ela pode ser apagada se o
merge dela no **commit de merge do PR** não mudar nada.

## Contexto
Pedido do Jonas: "deixar somente a main". Havia 30+ branches locais nos repos BTech e jogos, todas
com commits "à frente" por causa do squash. Testar contra a `main` atual reprovava quase todas: a
main mudou as mesmas linhas depois, e o merge simulado conflita mesmo sem nada novo.

## Detalhe
Prova, por branch (script em `~/.claude/cerebro/bin/limpar-branches-mergeadas.sh <ensaio|apagar> <repo>...`):
1. `git merge-tree --write-tree origin/main <branch>` == árvore da `origin/main` → já contida.
2. Senão, para cada PR MERGED cujo `headRefName` é a branch **ou** `headRefOid` é a ponta dela
   (pega duplicatas com outro nome), com o `mergeCommit` ancestral da main:
   `git merge-tree --write-tree <mergeCommit> <branch>` == árvore do `<mergeCommit>` → tudo entrou.
3. Sem prova → mantém e lista para decisão humana (ex.: conteúdo que entrou por PR de outro nome).

Sempre pula: branch padrão, branch atual, branch em worktree, branch com PR aberto. Worktree limpo
(`git status --porcelain` vazio) de sessão antiga pode ser removido com `git worktree remove` antes.
Ensaio antes de apagar. Branch remota com 0 commits à frente: `gh api -X DELETE repos/O/R/git/refs/heads/<b>`
(o GitHub recusa PR sem diferença).

Resultado em 2026-09-28: 32 branches locais e 3 remotas apagadas, nenhuma sem prova.

## Relacionado
- [[branch-mergeada-por-squash-e-apagada-no-remoto]]
- [[branch-de-pr-ja-mergeado-acumula-trabalho-novo-sem-perceber]]
- [[2026-09-28-btech-conferencia-fiscal-da-nota]]
