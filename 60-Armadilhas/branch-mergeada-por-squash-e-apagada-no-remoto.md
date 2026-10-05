---
tipo: armadilha
titulo: Continuar commitando numa branch ja mergeada por squash (e push que trava)
projeto: [BTech.NFe.Api, BTech.Web]
stack: [git, github]
tags: [tipo/armadilha, stack/git]
palavras-chave: [git push trava, squash merge, branch apagada, branch mergeada, cherry-pick, origin main, GIT_TERMINAL_PROMPT, osxkeychain, pr, security/checklist-20-regras]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Continuar commitando numa branch ja mergeada por squash (e push que trava)
## Resumo
Os repos BTech mergeiam PR por **squash**; a branch local continua existindo e parece viva, mas no GitHub ja foi apagada/absorvida — commitar nela e dar push recria historico ja mergeado.

## Contexto
2026-09-11: os dois repos estavam em `security/checklist-20-regras` com upstream configurado. No
remoto ela ja tinha virado `fcb3f53 Security/checklist 20 regras (#4)` (API) e `01fe39b ... (#1)`
(Web). O `git push` ficou parado sem saida por minutos.

## Detalhe
Antes de commitar/push em branch antiga:
```bash
git fetch origin --prune
git ls-remote --heads origin <branch>          # vazio = apagada no remoto
git diff --stat <tip-da-branch> origin/main    # vazio = squash identico ao conteudo da branch
```
Se foi mergeada: branch nova a partir de `origin/main` + `git cherry-pick` so dos commits novos.
Com arvores identicas o cherry-pick aplica limpo; confira `git diff --quiet <branch-antiga> HEAD`.

Push/ls-remote em sessao sem TTY: use `GIT_TERMINAL_PROMPT=0` e um timeout
(`perl -e 'alarm 150; exec @ARGV' git push ...`) — falha com erro em vez de travar esperando
credencial. Aqui a credencial vem do `osxkeychain`; o `gh` nao esta logado (sem criar PR pela CLI —
usar o link que o GitHub devolve no push).

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[separar-working-tree-em-commits-sem-add-p]]
