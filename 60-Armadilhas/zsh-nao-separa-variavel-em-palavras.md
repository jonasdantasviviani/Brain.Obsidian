---
tipo: armadilha
titulo: zsh nao separa variavel em palavras ($C com comando inteiro falha)
projeto: [todos]
stack: [zsh, bash, shell]
tags: [tipo/armadilha, stack/shell]
palavras-chave: [zsh, word splitting, variavel com comando, no such file or directory, docker compose -f, funcao shell, "$@", verificacao falsa]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# zsh nao separa variavel em palavras ($C com comando inteiro falha)
## Resumo
No zsh (shell do Mac do Jonas e da ferramenta Bash do Claude Code aqui), `C="docker compose -f a -f b"; $C up` tenta executar um arquivo chamado "docker compose -f a -f b".

## Contexto
Custou uma rodada de verificacao em [[BTech.NFe.Api]]: o `$C down -v` e o `$C up` falharam com
`no such file or directory`, mas os comandos seguintes rodaram contra o ambiente **antigo** e
pareceram confirmar a mudanca. Resultado enganoso, nao erro visivel.

## Detalhe
Use funcao:
```bash
c() { docker compose -f docker-compose.yml -f extra.yml "$@"; }
c down -v && c up -d --build
```
Alternativas: array (`cmd=(docker compose -f a); "${cmd[@]}" up`) ou `${=C}` (so zsh).
Scripts com `#!/usr/bin/env bash` rodados via `bash script.sh` nao tem o problema.

Licao geral: numa verificacao encadeada, falha de um passo anterior invalida os seguintes —
cheque o exit de cada passo (ou `set -e`) antes de ler o resultado.

## Relacionado
- [[docker-compose-nos-projetos]]
