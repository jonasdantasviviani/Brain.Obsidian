---
tipo: decisao
titulo: Um repositorio por jogo, com os documentos da linha copiados dentro
projeto: [RagdollGames, Games]
stack: [git, github]
tags: [tipo/decisao, stack/git, dominio/jogos]
palavras-chave: [repositorio, github, RabiscoGames, docs, roteiro, copia, links, autocontido, monorepo, organizacao]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-12
confianca: alta
---

# Um repositorio por jogo, com os documentos da linha copiados dentro
## Resumo
Na organizacao **RabiscoGames** cada jogo e um repositorio privado. Dentro dele vao o roteiro do
jogo (`docs/roteiro.md`, a **versao viva**) e uma **copia** dos documentos da linha
(`docs/serie/` na Ragdoll Games, `docs/progressivos/` na linha pessoal).

## Contexto
Jonas pediu "cada um um projeto separado" e depois "todos com seus roteiros e MDs". Os roteiros
linkam o tempo todo para documentos em comum (motor, identidade, kit de producao) e uns para os
outros.

## Decisao
- Repositorio **autocontido**: le-se sozinho no GitHub, sem depender da pasta local.
- Links reescritos pelo caminho resolvido: documento copiado -> link relativo; roteiro de outro
  jogo -> URL do repo daquele jogo (`github.com/RabiscoGames/<jogo>/blob/main/docs/roteiro.md`);
  alvo que nao existe no repo -> vira texto. Wikilinks do Obsidian viram texto.
- O roteiro original em `Repos/Games/ragdoll-games/jogos/` e `ideias/` ganhou aviso **"Versao
  viva: <repo>/docs/roteiro.md - edite la"**.

## Trade-off
- **Custo:** os documentos da linha estao duplicados em ate 5 repos e **vao divergir** se forem
  editados num so. A fonte deles continua sendo a pasta local `Repos/Games/`, **que nao esta
  versionada**.
- Saida se incomodar: um repo de planejamento (ex. `RabiscoGames/planejamento`) com a pasta
  `Repos/Games` inteira como fonte unica, e os repos dos jogos linkando para ele.
- Nao re-rodar a copia por cima dos repos depois que o roteiro for editado la - sobrescreveria a
  versao viva.

### Ao comecar o codigo num repo que so tinha documento
O `.gitignore` destes repos tem so o bloco de segredos, e `flutter create .` **nao mexe em
`.gitignore` existente**: `build/`, `.dart_tool/` e `.idea/` aparecem no `git status`. Acrescentar
o bloco Flutter antes do primeiro commit (feito no `ragdoll-go`, 2026-09-12). O `README.md`
sobreviveu ao `flutter create .`, mas vale backup antes.

## Relacionado
- [[2026-09-11-rabiscogames-versionar-jogos]]
- [[RagdollGames]]
- [[Games]]
