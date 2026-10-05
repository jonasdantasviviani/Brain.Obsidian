---
tipo: sessao
titulo: Rabisco Games - abertura com o logo riscado nos tres jogos
projeto: [RagdollGames]
stack: [flutter, dart, python]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [abertura, splash, logo riscado, barra de progresso, frases, som de caneta, capa, instalar.sh, import_lint, counter-ragdoll, ragdoll-go, need-for-ragdoll]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Abertura com o logo riscado nos tres jogos
## Resumo
A serie ganhou abertura de estudio: o logo da Rabisco nasce riscado numa folha com som de caneta,
a folha vira, e o jogo se apresenta com a capa, piadas e barra enchendo - instalada e conferida
nos tres jogos com codigo.

## Contexto
Jonas pediu (2026-09-17) a abertura para "os jogos do rabisco games", e no dia seguinte reforcou
pelo Counter-Ragdoll. A regra ficou registrada em
[[todo-jogo-da-serie-abre-com-o-logo-riscado]].

## O que foi feito
1. **Logo virado em risco**: `hub/ferramentas/tracos_do_logo.py`, stdlib pura, PNG -> 634 riscos
   de hachura na ordem em que a mao desenharia (boneco, RABISCO, GAMES). Como funciona:
   [[abertura-de-estudio-com-logo-riscado]]. Previa em `hub/site/img/logo-tracos.svg`.
2. **A abertura**: `presentation/abertura/` (ritmo, desenho do logo, cartaz com capa/frase/barra,
   frases) + `infrastructure/som/caneta_riscando*` (Web Audio sintetizado).
3. **Instalada nos tres**: Counter-Ragdoll (abertura por cima do menu, num `Stack`, com o
   `CanetaRiscando` criado na porta para o volume das opcoes valer desde o logo), Ragdoll GO e
   Need for Ragdoll (uma tela depois da outra). Capas do hub reduzidas para 560 px.
4. **Fonte unica + instalador** para os 81 jogos que faltam:
   `ragdoll-games/comum/abertura/instalar.sh` - ver [[instalador-de-codigo-copiado-entre-repos]].
5. **Documentado**: `comum/abertura-rabisco.md` (novo), README da serie e checklist de lancamento
   com a secao "Abertura do estudio" - original e as 5 copias em `docs/serie/`.
6. **De passagem**: o `counter-ragdoll` tinha a regra de camadas morta (`import_lint.yaml` solto,
   que o plugin 2.0 ignora, e nada em `analysis_options.yaml`). Migrei as regras para o
   `analysis_options.yaml` e apaguei o arquivo solto: `dart run import_lint` agora roda e passa.
   Ficou **de fora** a regra `forge2d_so_na_infra_presentation` - o
   `presentation/jogo/counter_ragdoll_game.dart` ainda fala forge2d direto, e ligar hoje
   reprovaria o jogo. Ver [[import-lint-2-config-e-regra-silenciosa]] e
   [[jogo-flame-estende-base-de-fisica-na-infra]].

### Ajustes no mesmo dia, depois de ver rodando
Jonas pediu tres mudancas: cartaz de **20 s** (era 2,8) com frase a cada 2,5 s, a **capa ocupando
a tela** com a barra por cima dela, e **som diferente do logo** no cartaz. Feitos no modelo e
reinstalados nos tres jogos. No caminho, uma licao: `BoxFit.cover` preenche a tela mas **corta o
letreiro do jogo** ("COUNTER RAGDOLL" virou "NTER RA" na foto de cena) - a capa entra inteira na
tela menos o pe, e o `multiply` funde as bordas na folha. O som do cartaz virou um tique seco de
papel a cada frase (`tiqueDePapel`), e o risco de caneta ficou so no logo.

## Como conferir
```bash
cd ~/Documents/Repos/Games/<jogo>
dart analyze lib test && dart run import_lint
flutter test --concurrency=1 test/presentation/abertura_test.dart
# fotos de cena (o que o jogador ve):
FOTO_DO_CARTAZ=/tmp/cartaz.png FOTO_DA_ABERTURA=/tmp/abertura.png \
  flutter test --concurrency=1 test/presentation/abertura_test.dart
```
Oito testes por jogo: a regua da tinta, riscos dentro da folha, o ritmo, o toque que nao pula o
logo, o toque que adianta, a barra que nao fecha antes do carregamento, e duas fotos de cena
(ver [[foto-de-cena-em-teste-flutter]]).

## Armadilhas do dia
- [[audio-do-navegador-espera-o-primeiro-gesto]] - a abertura toca antes de qualquer gesto.
- Hachura no angulo errado sai **tracejada**: o gerador testa 12 angulos por mancha e escolhe o
  de traco mais longo.
- **Copiar o `README.md` da serie por cima de `docs/serie/` desfaz a reescrita de links** de cada
  repo (roteiro de outro jogo aponta para o GitHub dele). Fiz isso e desfiz com
  `git checkout --`; o certo e inserir so as linhas novas em cada copia. Vale para os cinco
  repos - ver [[um-repo-por-jogo-com-docs-da-linha]].
- Rodar dois `flutter test` ao mesmo tempo no mesmo projeto derruba o compilador
  ("Dart compiler exited unexpectedly" / "Connection closed before test suite loaded"). Um por
  vez, com `--concurrency=1`; a maquina estava com ~260 MB livres.

## Fica para depois
- O letreiro da capa do Counter-Ragdoll escreve **"COUNTER RADGOLL"** (letras trocadas) e agora
  aparece na abertura do proprio jogo, nao so no hub - decisao de marca ainda pendente com o
  Jonas.
- A abertura nao roda em nenhum jogo do catalogo que ainda nao existe em codigo (81) - o
  instalador esta pronto para quando existirem.
- **Commitado em `main` nos seis repos** (nao empurrado): `hub` (a ferramenta do logo),
  `counter-ragdoll`, `ragdoll-go` (o jogo inteiro entrou no repo agora), `need-for-ragdoll`,
  `doodlecraft` e `grand-thief-ragdoll` (so documentos). Os commits do counter-ragdoll e do
  need-for-ragdoll levam junto trabalho que ja estava pendente na arvore de sessoes anteriores
  (chat da partida, fila de desenho, base de fisica) - esta dito na mensagem.
- A **fonte unica da abertura** (`ragdoll-games/comum/abertura/` + `instalar.sh`) continua **fora
  do git**, como toda a pasta `Repos/Games/ragdoll-games` - o instalador dos outros 81 jogos so
  existe nesta maquina. Ver [[um-repo-por-jogo-com-docs-da-linha]], que ja previa isso como custo.
- `counter-ragdoll/test/domain/chat_test.dart` (de sessao anterior) tem um **byte nulo cru** dentro
  de uma string (`Chat.limpar('\n<NUL>\t')`): o git trata o arquivo como binario e nao mostra
  diff. Trocar por `\u0000` resolve.

## Relacionado
- [[RagdollGames]]
- [[abertura-de-estudio-com-logo-riscado]]
- [[todo-jogo-da-serie-abre-com-o-logo-riscado]]
- [[instalador-de-codigo-copiado-entre-repos]]
- [[audio-do-navegador-espera-o-primeiro-gesto]]
- [[Rabisco-Hub]]
