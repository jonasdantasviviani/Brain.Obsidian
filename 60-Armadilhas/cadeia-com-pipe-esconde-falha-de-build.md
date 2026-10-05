---
tipo: armadilha
titulo: Comando em cadeia com pipe esconde falha (o codigo de saida e o do ultimo)
projeto: [todos]
stack: [bash, flutter]
tags: [tipo/armadilha, stack/bash]
palavras-chave: [pipe, exit code, pipefail, tail, build, cadeia, falso sucesso, ci]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Comando em cadeia com pipe esconde falha
## Resumo
`comando | tail -1` devolve o codigo de saida do **tail**, nao do comando. Numa cadeia
`a && b && c | tail -1`, uma falha em `c` passa por sucesso.

## Contexto
`counter-ragdoll`, 2026-09-11: `flutter build web --release 2>&1 | tail -1` dentro de uma
cadeia com `flutter test`. A build falhou ("Failed to compile application for the Web",
provavelmente por rodar junto com os testes) e a saida so mostrou as linhas dos passos
anteriores - conclui que tinha compilado.

## Como evitar
- `set -o pipefail` no comeco, ou
- rodar o comando pesado **sozinho**, sem `|`, e so depois filtrar a saida, ou
- conferir o artefato: `ls -la build/web/main.dart.js` (data) ou `grep -c "Built build/web"`.
- Desconfiar de cadeia longa: `dart format && analyze && test && build` num comando so
  tambem **serializa mal** - o build competindo com o teste foi o que derrubou.

## Relacionado
- [[2026-09-11-counter-ragdoll-muretas]]
