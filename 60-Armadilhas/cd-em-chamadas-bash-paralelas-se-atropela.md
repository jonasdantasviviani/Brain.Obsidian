---
tipo: armadilha
titulo: cd em chamadas Bash paralelas se atropela (comando roda no repositório errado)
projeto: [BTech.NFe.Api, BTech.Web]
stack: [git, shell]
tags: [tipo/armadilha, stack/git]
palavras-chave: [paralelo, cd, cwd, git switch, repositorio errado, git -C, caminho absoluto, claude code]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# cd em chamadas Bash paralelas se atropela
## Resumo
Duas chamadas Bash disparadas em paralelo, cada uma começando com `cd <repo>`, compartilharam o diretório de trabalho: a do Web executou dentro da API e relatou "Web na main: 862cffd" — hash e mensagem do commit da API. O Web continuava em outra branch.

## Contexto
[[BTech.NFe.Api]] e [[BTech.Web]], 2026-09-12, trocando os dois repositórios para a `main` ao mesmo tempo.

## Detalhe
- Sintoma que denunciou: a saída trazia o hash e a mensagem do **outro** repositório.
- Em chamada paralela, nunca depender de `cd`. Usar caminho absoluto:
  `git -C /abs/repo switch main`, `npm run lint --prefix /abs/repo`,
  `npx --prefix /abs/repo tsc -p /abs/repo/tsconfig.json --noEmit`,
  `docker compose -f /abs/docker-compose.yml --project-directory /abs up -d`.
- Conferir o resultado com `git -C <repo> rev-parse --show-toplevel` + `branch --show-current`.
- Outra do mesmo dia: `${PIPESTATUS[0]}` é de bash; o shell aqui é **zsh** e devolve vazio.
  Para pegar o código de saída, redirecionar para arquivo e usar `$?` sem pipe.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
