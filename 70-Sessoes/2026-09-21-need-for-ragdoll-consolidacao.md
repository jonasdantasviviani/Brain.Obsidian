---
tipo: sessao
titulo: Consolidacao do trabalho orfao - progresso, campeonato, rivais, carros
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, jogo/ragdoll]
palavras-chave: [consolidacao, progresso, campeonato, rivais, garagem, commit por pacote]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: media
---

Agentes paralelos morreram por limite de uso e deixaram trabalho sem commit. Recuperado em 5 commits por pacote (progresso e campeonato, rivais, carros, largada, docs). Ver [[need-for-ragdoll]] em 10-Projetos.

- Lint pos-`dart fix`: `avoid_redundant_argument_values` remove o argumento de canal 0 e deixa constante sem uso (unused_field): confira a analise depois do `corrigir_lint.sh`.
- `containsAll(listaComRepetidos)` falha contra um conjunto: use `.toSet()` no esperado.
- Testes de `infrastructure/` nao rodam no espelho so-dominio; escreva, mas registre como nao verificado.
- Teste de simulacao com CarroDePapel roda 5 rivais x 3 niveis x 2 voltas em menos de 1 s.
