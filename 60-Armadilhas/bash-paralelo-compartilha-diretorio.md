---
tipo: armadilha
titulo: Comandos Bash em paralelo dividem o diretorio - o cd de um vale para o outro
projeto: [todos]
stack: [claude-code, bash]
tags: [tipo/armadilha, stack/claude-code]
palavras-chave: [bash, paralelo, cd, diretorio de trabalho, cwd, repositorio errado, sonda, contaminacao, subshell, caminho absoluto, git -C]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# Bash em paralelo divide o diretorio
## Resumo
Duas chamadas Bash na mesma resposta se comportaram como um shell so: o `cd` de uma vazou para a
outra, e o segundo comando rodou **no repositorio errado sem erro nenhum**.

## Contexto
2026-09-12, `need-for-ragdoll` e `ragdoll-go` em paralelo, cada comando comecando com
`cd <repo> && ...`. O do `need-for-ragdoll` criou sonda, rodou `dart analyze` e `flutter test`
**dentro do `ragdoll-go`** - denunciado por "+32 testes" e pelo `git status` do outro projeto. Na
mesma sessao, um `cat counter-ragdoll/...` relativo ja tinha falhado com `No such file` pelo mesmo
motivo, e o painel avisava "Primary working directory changed".

## Detalhe
### Sinais
- Numero de testes, nome do projeto em "Analyzing X" ou `git status` que sao **do outro** repo.
- `No such file` num caminho relativo que existe.
- Experimento com resultado incoerente (sonda que some ou aparece duas vezes).

### Regra
- Em chamadas paralelas, **nenhum `cd` no topo**: caminho absoluto, `git -C <repo>`, ou tudo dentro
  de subshell `( cd /abs/repo && ... )` - o `cd` do subshell nao volta para o shell pai.
- Passos que **mexem em arquivo** (sonda, backup e restauro de config) em repos diferentes: numa
  chamada so, em sequencia, com `for p in ...; do ( cd $G/$p && ... ); done`.
- Imprimir `$(pwd)` no inicio de cada bloco e conferir no resultado.
- Depois de uma contaminacao, **refazer a verificacao em sequencia** - o resultado paralelo nao vale.
- Checagem de duplicata no vault por palavra solta casa com **links** `[[...]]` de outras notas:
  buscar pelo nome do arquivo, nao pelo texto.

## Relacionado
- [[cadeia-com-pipe-esconde-falha-de-build]]
- [[import-lint-2-config-e-regra-silenciosa]]
