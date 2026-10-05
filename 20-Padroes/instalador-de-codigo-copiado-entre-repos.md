---
tipo: padrao
titulo: Instalador para codigo copiado entre repos (um pacote diferente em cada)
projeto: [RagdollGames]
stack: [bash, python, flutter, dart]
tags: [tipo/padrao, stack/bash, dominio/jogos]
palavras-chave: [copiar codigo entre repos, PACOTE, placeholder, instalar.sh, import ordering, directives_ordering, monorepo, autocontido, sem pacote compartilhado]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: media
---

# Instalador para codigo copiado entre repos
## Resumo
Quando a decisao e **um repo por jogo sem pacote compartilhado**, o codigo comum vive num modelo
com `package:PACOTE/` e um `instalar.sh` copia trocando o nome - e **reordenando os imports**.

## Contexto
Feito em 2026-09-18 para a abertura da [[RagdollGames]] (`comum/abertura/instalar.sh`), sob a
decisao [[um-repo-por-jogo-com-docs-da-linha]]: repos autocontidos, nada de dependencia interna.
Sao 84 jogos previstos - copiar a mao 84 vezes nao e opcao.

## Detalhe
- Modelo em `Repos/Games/ragdoll-games/comum/abertura/{presentation,infrastructure,test}` com
  `package:PACOTE/...` nos imports. O modelo **nao compila** onde esta, de proposito: quem compila
  e a copia dentro do jogo.
- `./instalar.sh <pasta-do-jogo> <nome_do_pacote>`: confere que o `pubspec.yaml` declara aquele
  `name:`, copia com `sed`, e **imprime o que falta fazer a mao** (pubspec, capa, frases, main).
- **Arquivo do jogo nao e sobrescrito**: `frases_da_abertura.dart` ja existente e mantido.
- **A ordem dos imports muda com o nome do pacote.** `directives_ordering` (very_good_analysis)
  exige ordem alfabetica, e `package:counter_ragdoll/` vem antes de `package:flutter/` enquanto
  `package:ragdoll_go/` vem depois. O instalador reordena o bloco `import 'package:...'` num
  trecho python depois do `sed` - sem isso, metade dos jogos reprova no lint.
- Consertar bug do comum = consertar no modelo e rodar o instalador em cada jogo. Quem edita a
  copia perde na proxima instalacao (menos as frases).

## Relacionado
- [[um-repo-por-jogo-com-docs-da-linha]]
- [[abertura-de-estudio-com-logo-riscado]]
- [[RagdollGames]]
