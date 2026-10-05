---
tipo: projeto
titulo: Ragdoll Games (estudio Rabisco)
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/projeto, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [ragdoll, rabisco, caneta, papel, fisica, forge2d, flame, parodia, satira, navegador, web, franquia, abertura, splash, logo riscado]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-22
confianca: media
---

# Ragdoll Games (estudio Rabisco)
## Resumo
Serie de jogos desenhados a caneta numa folha de papel, com personagem sempre ragdoll, cada um
satirizando o **genero** de uma grande franquia.

## Contexto
Segunda linha do [[Games]], criada em 2026-09-09. Estudio: **Rabisco**. Serie: **Ragdoll Games**.
Roda em **navegador e app** - o traco de rabisco (linha vetorial, sem textura nem sprite) e o que
viabiliza a web.

## Detalhe

### Onde fica
`~/Documents/Repos/Games/ragdoll-games` - so documento.
`README.md` · `catalogo-de-franquias.md` · `comum/{identidade-rabisco,motor-ragdoll,web-e-app,nomes-e-marcas}.md` ·
`jogos/0N-*.md` (cada um com **roteiro completo**) ·
`producao/{00-como-comecar,01-estrutura-do-projeto,02-design-tokens,03-primeiro-sprint,04-checklist-de-lancamento}.md`

### O que ja existe em codigo
| Jogo | Onde | Estado |
| --- | --- | --- |
| Need for Ragdoll | `Repos/Games/need-for-ragdoll` (repo privado `RabiscoGames/need-for-ragdoll`) | **dias 1 a 7 do sprint** (2026-09-20): abertura da Rabisco, e o jogo virou **vista de cima sem gravidade** - carro com aceleracao, freio, re, esterco e atrito lateral, **drift pelo circulo de atrito**, rastro de pneu na folha, **motorista ragdoll preso ao banco por amarras que arrebentam na batida**, teclado e toque abstraidos em `Comando`; **menu, ajustes salvos e pausa** (PR #1). **Roadmap inteiro na `main`** (PR #6 e #7, 2026-09-22, `e4d927e`): 4 pistas, 5 rivais, campeonato, nitro, som, garagem - **prototipo jogavel** no hub. CI da `main` com `analise` vermelha (4 `discarded_futures`). Clone de `~/Documents` defasado - atualizar com fetch, nao reclonar |
| Counter-Ragdoll | `Repos/Games/counter-ragdoll` (repo privado `RabiscoGames/counter-ragdoll`) | 1a pessoa por raycasting, menu do CS 1.6, rodadas + economia + loja, bala com altura, **headshot mata sem capacete** (com capacete x2 e parte), bots com arma, cobertura e navegacao, **placar no TAB**, seis cenarios com **porta, janela, piso e piscina em que se entra**, teto desenhado, sobe em balcao e engradado, **sniper com luneta**, **granadas explosiva/cegante/fumaca**, **modo bomba** nos mapas de_, e **lobby de multijogador** ainda sem rede. Mapa completo em `ROADMAP.md` |
| Ragdoll GO | `Repos/Games/ragdoll-go` | **captura jogavel no navegador** (2026-09-12): mundo cilindrico 360 graus, 6 bichos ragdoll desenhados, arremesso por flick, captura com 1 a 3 bolinhas, caderno em memoria, 32 testes. Falta fundo de camera, bussola e caderno salvo |

Jonas quer o Counter-Ragdoll **fiel ao CS 1.6**, so que em rabisco: rodadas, economia, loja,
bomba, granadas, agachar - tudo mapeado em `ROADMAP.md` para construir aos poucos.
Servidor de teste local: `localhost:5125`, **sem cache** (Ragdoll GO em `localhost:5130` - **uma porta por jogo**, a 5125 fica ocupada pelo Counter-Ragdoll) (ver [[http-server-do-python-serve-build-velho]]).

Counter-Ragdoll virou **primeira pessoa** por pedido do Jonas em 2026-09-09. Isso trocou a
tecnica: raycasting sobre grade 2D, ver [[raycasting-em-flutter]]. E o jogo mais barato da serie
porque em primeira pessoa nao ha corpo do jogador para desenhar.

### Como comecar a codar
Primeiro jogo: **Need for Ragdoll**. Passo a passo em `producao/00-como-comecar.md`:
`flutter create --org com.rabisco --platforms=web,android,ios`, depois
`flutter pub add flame flame_forge2d flutter_bloc get_it hive_ce`.

**Os commits 5 a 8 sao o projeto inteiro**: esqueleto com juntas limitadas · desenho de caneta
por cima · tremor a 10 fps · `consciencia`. Se esses quatro ficarem bons, os cinco jogos existem.

Sprint 1 = 10 dias ate jogavel no navegador. Criterio de sucesso: alguem joga 2 min sem
explicacao e ri pelo menos uma vez.

### As regras da serie
1. Tudo a caneta, com **tremor** (contorno redesenhado a 8-12 fps enquanto o jogo roda a 60).
2. Personagem **sempre** ragdoll - assinatura, nao efeito.
3. A fisica e a piada, nao o obstaculo.
4. Um motor, todos os jogos.
5. Navegador primeiro, app depois.
6. Nome pela formula: **titulo original, na lingua dele, uma palavra trocada por `Ragdoll`**
   (ou `Doodle`, quando a piada e o desenho) - ver [[nomes-por-alusao-em-parodia]].
7. **Todo jogo abre com o logo da Rabisco sendo riscado**, e so depois se apresenta com capa,
   barra e piadas - ver [[todo-jogo-da-serie-abre-com-o-logo-riscado]] e
   [[abertura-de-estudio-com-logo-riscado]]. Fonte unica em `comum/abertura/` com `instalar.sh`;
   instalada nos tres jogos com codigo em 2026-09-18.

### Os cinco primeiros
| Nome | Ref. interna (nunca publica) | | Esforco |
| --- | --- | --- | --- |
| Counter-Ragdoll | tiro tatico por rodada | 🟡 | ⭐⭐⭐ |
| Need for Ragdoll | corrida arcade | 🟡 | ⭐⭐ (fazer primeiro) |
| Grand Thief Ragdoll | mundo aberto | 🔴 | ⭐⭐⭐⭐⭐ |
| Doodlecraft | sandbox de blocos | 🟢 | ⭐⭐⭐⭐ |
| Ragdoll GO | cacar criatura | 🟢 | ⭐⭐⭐⭐ |

Mais 60 franquias no `catalogo-de-franquias.md` (Fall Ragdolls, Resident Ragdoll, Doodle Portal,
Ragdoll FC, Untitled Ragdoll Game, God of Ragdoll, Ragdoll Royale...), cada uma com nivel de
risco e, quando 🔴, o **nome reserva** ja escolhido.

Ordem: **Need for Ragdoll → Counter-Ragdoll → jogos pequenos do catalogo → Grand Thief Ragdoll**.

### Stack
`flame` + `flame_forge2d` (Box2D em Dart). Aqui a decisao
[[flutter-puro-sem-engine-nos-jogos]] **nao vale** - ragdoll e simulacao de corpo rigido.
Ver [[ragdoll-em-flutter-com-forge2d]].

Web: CanvasKit, orcamento de 5 MB, audio so apos o primeiro toque, entrada abstraida em
`Comando` desde o inicio (teclado + toque).

**Regra de camadas (`import_lint 2.0`)** - ligada em 2026-09-12 no `need-for-ragdoll` e no
`ragdoll-go`, **zero violacao**: 10 regras dentro do `analysis_options.yaml`, `severity: "error"`,
plugin com `diagnostics: import_lint: true`. **Portao: `dart run import_lint`** (sai com 1). O plugin
so mostra `info` na IDE e no `dart analyze`; o `flutter analyze` nao mostra nada. O jogo Flame estende
`infrastructure/fisica/jogo_com_fisica.dart` - ver [[jogo-flame-estende-base-de-fisica-na-infra]] e
[[import-lint-2-config-e-regra-silenciosa]]. Documentos `00`, `01` e `04` da serie corrigidos no
original e nas 5 copias. O `counter-ragdoll` foi ligado ao portao em 2026-09-18 (regras migradas para o
`analysis_options.yaml`, `import_lint.yaml` solto apagado, `dart run import_lint` passando) -
**sem** a regra `forge2d_so_na_infra_presentation`, porque o
`presentation/jogo/counter_ragdoll_game.dart` ainda fala forge2d direto. **Pendente:** mover isso
para uma base na infra e ligar a regra; e o `stack-flutter.md` dos Progressivos ainda promete que
o `import_lint.yaml` reprova.

### Hub e capas
Todos os jogos da serie estao no hub **[[Rabisco-Hub]]** (`Repos/Games/hub`, repo privado
`RabiscoGames/hub`, 2026-09-12): pagina estatica que aponta para cada jogo em `/<slug>/`; local com
`python3 servir.py` na porta **5120**. Jonas vai fazer **todos os 84 jogos do catalogo** - o hub ja
tem um quadrado por jogo, com filtro pelas 11 categorias, lido de `dados/jogos.json`. Jogo que
avanca: mudar `estado` no JSON e rodar `python3 gerar.py`; a pasta do repo do jogo tem o nome do slug.

**Nomes repetidos no catalogo** (aguardando decisao): *Super Ragdoll Bros* em Luta e esporte e em
Plataforma (slugs `super-ragdoll-bros-luta` e `super-ragdoll-bros`); *Ragdoll Royale* e jogo de
Casual **e** reserva do Ragdollnite; *The Elder Ragdolls* e jogo de RPG **e** reserva do Ragdollrim.

**Capas dos jogos** - Jonas mandou em 2026-09-12 (`~/Downloads/<Nome>.png`), ja estao no hub em
`img/jogos/`. Conflitos com `comum/nomes-e-marcas.md` ("proibido em qualquer nivel: personagem,
criatura, mapa e item identificaveis; logo, fonte e paleta do original"), **aguardando decisao dele**:

| Capa | Conflito |
| --- | --- |
| Counter-Ragdoll | letreiro escreve **"COUNTER RADGOLL"** (letras trocadas); pichacao de "A" circulado lembra marcacao de bombsite |
| Grand Thief Ragdoll | letreiro diz **"grand theft ragdoll"** (o nome decidido e *Thief*; *Theft* deixa "Grand Theft" intacto) e o "ll" le "il"; fonte e mosaico de quadros da capa original |
| Ragdoll GO | letreiro amarelo com contorno azul do original, bola vermelha e branca no "O" e na tela do celular, bone vermelho, simbolo ™ |
| Doodlecraft | personagem, criaturas (a verde, esqueleto com arco, porco, vaca, abelha) e letreiro de blocos do original; lapis de cor, fora da caneta monocromatica |
| Need for Ragdoll | sem conflito de marca |

### Seguranca
Jogo local, sem conta, sem back-end: nada a proteger. Web estatica ainda pede
[[18-security-headers]] (CSP vale mesmo sem back-end). Se entrar placar online, valem
[[22-cors-restritivo]] e [[14-validacao-dos-inputs]].
**Ragdoll GO e a excecao**: camera + localizacao. Decisao registrada - fazer so a "versao A"
(camera e bussola, sem GPS e sem mapa), que nao coleta dado pessoal e nao aciona LGPD.

## Versionamento
Organizacao **github.com/RabiscoGames** (criada pelo Jonas em 2026-09-11), **um repositorio
privado por jogo**, publicados em 2026-09-11: `counter-ragdoll`, `need-for-ragdoll`,
`grand-thief-ragdoll`, `doodlecraft`, `ragdoll-go` (remoto `origin`, branch `main`). Documentos da
serie copiados em `docs/serie/` de cada um - ver [[um-repo-por-jogo-com-docs-da-linha]]. Jogo novo: `git init`, auditar
segredos, `gh repo create RabiscoGames/<jogo> --private --source . --remote origin --push`. O roteiro
de cada jogo vive em `docs/roteiro.md` do repo; os documentos da serie ficam locais em
`Repos/Games/ragdoll-games/`. Ver [[2026-09-11-rabiscogames-versionar-jogos]].

## Relacionado
- [[Games]]
- [[abertura-de-estudio-com-logo-riscado]]
- [[todo-jogo-da-serie-abre-com-o-logo-riscado]]
- [[instalador-de-codigo-copiado-entre-repos]]
- [[2026-09-18-rabisco-abertura-dos-jogos]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[nomes-por-alusao-em-parodia]]
- [[parodia-precisa-aludir]]
- [[flutter]]
- [[import-lint-2-config-e-regra-silenciosa]]
- [[jogo-flame-estende-base-de-fisica-na-infra]]
- [[mundo-cilindrico-com-fisica-2d]]
- [[forge2d-viewfinder-zoom-multiplica-meters-to-pixels]]
