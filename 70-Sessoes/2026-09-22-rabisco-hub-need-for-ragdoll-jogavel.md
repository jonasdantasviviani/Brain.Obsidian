---
tipo: sessao
titulo: Rabisco Games hub - servir.py compila os jogos antes de subir, e o texto do NFR some pela CSP
projeto: [Rabisco-Hub, RagdollGames]
stack: [python, html, flutter, git]
tags: [tipo/sessao, stack/html, dominio/jogos, jogo/ragdoll]
palavras-chave: [build antes de subir, anotacao local, bilhete do servidor, csp apaga o texto, need for ragdoll, need for speed, prototipo jogavel, estado jogavel, obras, clonar de novo, clone defasado, build velho, artefato do ci, jogo-web, gh run download, icloud, dataless, disco cheio, hub/hub, clone aninhado]
origem: claude-code
criado: 2026-09-22
atualizado: 2026-09-22
confianca: alta
---

# Need for Ragdoll vira "Protótipo jogável" no hub

## Resumo
Jonas: "Ajuste o need for speed, ja tenho um prototipo jogavel, preciso clonar ele de novo?".
Resposta: **nao precisa clonar**. O cartao do hub foi para `jogavel`, e o build local depende do
artefato do CI.

## Contexto
O cartao ainda dizia `obras` / "Por enquanto, so o motorista e a fisica". O roadmap inteiro ja
estava na `main` do GitHub (PR #6 e #7, squash, `e4d927e`), mas:

- o clone em `~/Documents/Repos/Games/need-for-ragdoll` parou em `roadmap` `9bdc93c` (main local
  `eca8742`, anterior ao PR #1) e **nem tem o `e4d927e`**. Sem fetch;
- o `build/web` desse clone, que o `servir.py` do hub serve em `/need-for-ragdoll/`, e de
  **2026-09-20 22:14**: so carro e motorista;
- o trabalho de verdade ficou em `/tmp/claude-501/nfr` e `nfr-pr` (arvore identica a `origin/main`,
  conferido com `git diff --stat`). `/tmp` some no reboot;
- `/tmp/claude-501/jogo-web` e o artefato do CI de 2026-09-21 23:41, **antes** da correcao do
  travamento no capotamento. Nao serve.

## Detalhe
- `dados/jogos.json`: `estado: jogavel`, pitch "Campeonato de quatro pistas rabiscadas na folha,
  contra cinco rivais..." (conferido em `lib/domain/progresso/catalogo.dart`: 5 rivais, 4 pistas de
  corrida + patio), nota "Setas ou WASD; no celular, o dedo na tela". `gerar.py` e `--conferir` saem 0.
- **Build local impossivel hoje**: disco em 99% (4 GB livres) e o SDK do Flutter evictado pelo
  iCloud (1012/1013 do `dart-sdk`, 294/294 do `flutter_web_sdk`) - ver
  [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]. Caminho: artefato `jogo-web` do run
  `35725507046` (job `build` verde, sha `e4d927e`, 13,6 MB, expira 2026-09-27):
  ```bash
  gh run download 35725507046 -R RabiscoGames/need-for-ragdoll -n jogo-web -D <destino>
  ```
  Baixado com o ok do Jonas na segunda parte da sessao.
- **CI da `main` vermelho** so no job `analise`: 4 `discarded_futures` (aparecem 2x cada, "8 issues")
  em `lib/presentation/abertura/abertura_rabisco.dart:132` e `lib/presentation/telas/podio.dart:61,74,173`.
  `testes`, `fisica` e `build` verdes. Correcao: `unawaited(...)`.
- `hub/hub/`: clone aninhado do proprio hub, nao versionado, parado em `52e4a22`, sem mudanca
  local (so falta o commit das capas). Nao vai ao ar (`site/` e so o que publica). Nao apaguei.

## Segunda parte - "o jogo tem q estar buildado e executando"
Jonas: "coloque no hub uma anotacao de que esta pronto para rodar local ... Ele deve tentar
buildar os jogos antes de abrir o hub".

- `servir.py` ganhou o passo de build e o bilhete na pagina - ver
  [[servidor-do-hub-compila-os-jogos-antes-de-subir]]. README com a saida nova e `--sem-build`.
- Jonas rodou o `git fetch origin main:main && git switch main` que eu deixei no terminal: o clone
  de `~/Documents` ja esta em `e4d927e`.
- **Minha tentativa de build apagou o `build/` do need-for-ragdoll** (o `flutter build` limpa a
  pasta antes de compilar e travou em seguida). Era o build velho de 20/09 - mas o jogo ficou sem
  nada. Com a autorizacao do Jonas, `gh run download 35725507046 -n jogo-web` trouxe o build de
  `e4d927e` (40 MB no disco).
- **O jogo abre e nao mostra texto**: a CSP do proprio jogo barra a Roboto do gstatic. Causa,
  prova e correcao em [[csp-self-apaga-todo-o-texto-do-flutter-web]]. Conferido na tela: abertura
  da Rabisco roda, menu desenha a marca-texto e para; sem a `<meta>` de CSP o menu aparece inteiro.
- `flutter --version` **nao volta** nesta maquina (matei em 143). Por isso o `servir.py` prova o
  compilador em 60 s, vigia CPU parada (90 s) e desiste dos outros quando um trava.
- Um `servir.py` do Jonas ficou rodando na 5120 desde as 19:13, **com o codigo velho**: reiniciar
  para ganhar build automatico e bilhete.

## Terceira parte - o PR da fonte e a cara do jogo (2026-09-23)
- **PR #11 aberto e verde no que importa**: `assets/fontes/` com as Roboto do cache do SDK
  (Apache-2.0 + licenca empacotada), `pubspec.yaml` com `family: Roboto` (o nome e o
  mecanismo), teste em `test/lancamento/` e passo novo no job `build` que confere o
  `FontManifest.json` do artefato. **O CI compilou e o passo da fonte passou** - `testes`,
  `fisica` e `build` verdes. `analise` (4 `discarded_futures`) e `osv` ja falhavam em todas
  as branches, inclusive na `main`. Mecanismo completo em
  [[csp-self-apaga-todo-o-texto-do-flutter-web]] e em `docs/decisoes/fonte.md` do repo.
- Antes do PR, destravei o Jonas no ato: as Roboto foram para `build/web/assets/fontes/` e a
  familia entrou no `FontManifest.json` do build baixado. O menu apareceu inteiro com a CSP
  ligada e console limpo - **prova empirica da correcao antes de escrever o patch**.
- Jonas olhou o jogo e disse que **nao parece Need for Speed, parece um ferrorama**. Estudo
  com 11 agentes (os 3 ceticos refutaram o juiz) virou
  [[cara-de-need-for-speed-underground]] - EM ABERTO, decisao dele.
- **Limite de uso da conta matou juiz e ceticos dos dois workflows**; `resumeFromRunId`
  recuperou tudo o que ja tinha rodado e so refez o que faltava. Ver
  [[agentes-paralelos-perdem-tudo-sem-commit]].

### Pendente
Decidir a cara do jogo ([[cara-de-need-for-speed-underground]]); mergear o PR #11; commit do
hub; `analise` e `osv` vermelhos ha varias branches; destino do `hub/hub/`; disco em 98%.

## Relacionado
- [[Rabisco-Hub]]
- [[RagdollGames]]
- [[2026-09-21-need-for-ragdoll-roadmap-em-paralelo]]
- [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[servidor-do-hub-compila-os-jogos-antes-de-subir]]
- [[csp-self-apaga-todo-o-texto-do-flutter-web]]
- [[cara-de-need-for-speed-underground]]
