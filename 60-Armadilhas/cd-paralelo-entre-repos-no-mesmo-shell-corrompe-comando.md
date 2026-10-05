---
tipo: armadilha
titulo: cd + comando em paralelo no mesmo shell vaza cwd entre repositorios diferentes
projeto: [BTech.Web, BTech.NFe.Api, todos]
stack: [bash, git, claude-code]
tags: [tipo/armadilha, stack/bash, ferramenta/claude-code]
palavras-chave: [cd paralelo, working directory, race condition, bash tool, git push repo errado, multiplos repositorios, cwd compartilhado]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: alta
---

# cd + comando em paralelo no mesmo shell vaza cwd entre repositorios diferentes
## Resumo
O Bash tool do Claude Code mantém working directory persistente entre chamadas, mas **não** isola
cada chamada em seu próprio shell. Dois `cd /repoA && comando` e `cd /repoB && comando` disparados
em paralelo (mesma resposta, duas tool calls) competem pelo mesmo cwd — o segundo `cd` pode vencer a
corrida antes do primeiro comando terminar, fazendo o comando do repoA rodar dentro do repoB (ou
vice-versa).

## Contexto
Em 2026-09-16, ao comitar e abrir PR em dois repositórios (`BTech.NFe.Api` e `BTech.Web`) na mesma
sessão, dois `cd <repo> && git log --oneline / git remote -v` foram disparados em paralelo. O
resultado do segundo comando (esperado ser do `BTech.Web`) veio **idêntico** ao do primeiro
(`BTech.NFe.Api`) — mesmo log, mesmo remote. Rodar de novo sozinho (sem paralelismo) confirmou o
repo certo. Mais grave: um `git push -u origin <branch-do-Web>` disparado em paralelo com um push do
Api de fato tentou empurrar a branch do Web para o remote do Api (`does not match any` — falhou sem
dano, mas poderia ter sido um push válido no repo errado se o nome de branch coincidisse).

## Detalhe
Isso contraria a expectativa de que comandos em repos diferentes são "independentes" e portanto
seguros para paralelizar — [[trabalhar-o-maximo-possivel-em-paralelo]] vale para tarefas realmente
isoladas (ex. duas leituras, duas buscas), mas **não** para sequências `cd X && ...` em shells que
compartilham estado de working directory. Regra: quando a tarefa envolve mais de um repositório
git na mesma sessão (commit, push, gh pr create, etc.), rodar os comandos de git **sempre
sequenciais**, um de cada vez, mesmo que pareçam independentes. Paralelizar é seguro para leituras
puras (Read, grep) mas não para qualquer coisa que dependa de `cd` prévio no mesmo Bash tool.

## Relacionado
- [[BTech.Web]]
- [[BTech.NFe.Api]]
- [[trabalhar-o-maximo-possivel-em-paralelo]]
