---
tipo: armadilha
titulo: PR mergeado no meio da sessão — trabalho novo continuou empilhando na mesma branch local
projeto: [BTech.NFe.Api, BTech.Web]
stack: [git, github]
tags: [tipo/armadilha, stack/git]
palavras-chave: [pr mergeado, branch antiga, git stash, checkout main, trabalho perdido quase, squash merge]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: alta
---

# PR mergeado no meio da sessão — trabalho novo continuou empilhando na mesma branch local
## Resumo
Numa sessão longa (mesmo dia, várias rodadas), o Jonas mergeou os PRs #35 (API) e #18 (Web) entre
uma rodada e outra. Eu continuei trabalhando **sem recriar a branch** — três rodadas inteiras de
código novo (Focus NFe real, dashboard/notificações, auditoria) ficaram como alterações não
commitadas em cima de uma branch local cujo PR já estava fechado (merged) no GitHub. Só percebi ao
tentar commitar de novo, checando `gh pr view` antes de dar push.

## Contexto
Sessão de 2026-09-16, mesmo fio de conversa desde a manhã. Cada rodada anterior já tinha aberto PR
e o Jonas os mergeou fora do meu controle (normal — é o repositório dele). Eu segui reusando
`git checkout <branch-antiga>` implicitamente (nunca saí dela) em vez de checar o estado da branch
antes de cada nova leva de trabalho.

## Detalhe
**Por que isso quase deu problema**: continuar commitando numa branch cujo PR já foi mergeado por
squash recria histórico que o GitHub já absorveu — mesmo padrão do
[[branch-mergeada-por-squash-e-apagada-no-remoto]], só que desta vez capturado *antes* do push, não
depois. Se eu tivesse dado `git push` direto (sem checar), teria ou falhado esquisito ou reaberto
um PR fechado com histórico duplicado.

**Correção segura, sem perder nada**:
1. `git stash push -u -m "descrição"` — inclui untracked, guarda tudo.
2. `git checkout main && git pull origin main` — pega o merge que já aconteceu.
3. `git checkout -b <branch-nova>` — a partir do main atualizado.
4. `git stash pop` — reaplica o trabalho pendente em cima da base certa. Nos dois repos essa etapa
   fez merge automático sem conflito (`Auto-merging package.json` etc.), mas **vale sempre checar
   `git status` com atenção depois** — ver achado seguinte.

**Achado extra pego só de olhar `git status` com cuidado**: o `stash pop` do Web trouxe 4 arquivos
fantasmas (`page 2.tsx` — cópia de sincronização do iCloud, mesma família de
[[icloud-evicta-node-modules-e-tsc-trava]] mas o oposto: em vez de evictar, o iCloud *duplicou*
arquivo durante uma janela de escrita concorrente do git). Eram bytes idênticos ao arquivo real,
sem conteúdo próprio — descartados. Lição: depois de qualquer `stash pop`/`checkout` num repo
dentro de `~/Documents`, olhar a lista de arquivos staged por inteiro antes de commitar, não só
confiar que "modified"/"added" bate com o que era esperado.

**Regra pra próxima sessão longa com múltiplos merges no meio**: antes de continuar trabalhando ou
commitar depois de qualquer pausa longa, rodar `gh pr view <numero>` (ou `git log origin/main`) pra
confirmar se a branch atual ainda está "viva" antes de assumir que dá pra só continuar nela.

## Relacionado
- [[branch-mergeada-por-squash-e-apagada-no-remoto]]
- [[icloud-evicta-node-modules-e-tsc-trava]]
- [[cd-paralelo-entre-repos-no-mesmo-shell-corrompe-comando]]
- [[BTech.NFe.Api]]
- [[BTech.Web]]
