---
tipo: projeto
titulo: Games
projeto: [Games, RagdollGames]
stack: [flutter, dart]
tags: [tipo/projeto, stack/flutter, dominio/jogos]
palavras-chave: [jogo, game, idle, incremental, clicker, merge, roguelite, offline, frota, um botao]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-11
confianca: media
---

# Games
## Resumo
Jogos mobile **progressivos** (idle/incremental) em Flutter, 100% offline - projeto pessoal.

## Contexto
Comecou em 2026-09-09 como levantamento de ideias. Ainda **nao existe codigo**: hoje a pasta e
so planejamento. Regra do genero: sem servidor, sem conta, sem rede, sem monetizacao na V1.

## Detalhe

### Duas linhas
| Linha | O que e | Tecnica | Alvo |
| --- | --- | --- | --- |
| Progressivos | idle / incremental | Flutter puro, sem engine | app, 100% offline |
| [[RagdollGames]] | satira rabisco de franquias, fisica ragdoll | `flame` + `flame_forge2d` | navegador **e** app |

Estudio das duas: **Rabisco**.

### Onde fica
`~/Documents/Repos/Games` - so documento. A segunda linha vive em
`~/Documents/Repos/Games/ragdoll-games/` (ver [[RagdollGames]]). Quando uma ideia virar codigo, o app nasce em
`Games/<nome-do-jogo>/` como projeto Flutter proprio, e a pasta de docs continua sendo o
planejamento dele.

| Arquivo | Serve para |
| --- | --- |
| `README.md` | indice das 6 ideias, esforco, status, ordem sugerida |
| `comum/motor-idle.md` | tick, save, ganho offline, prestige, numeros grandes |
| `comum/stack-flutter.md` | camadas, pacotes, qualidade, seguranca |
| `comum/design-de-progressao.md` | as 3 engrenagens, curva de custo, ritmo |
| `ideias/0N-*.md` | uma ideia cada: pitch, loops, economia, MVP, riscos |

### As seis ideias
| # | Jogo | Genero | Esforco |
| --- | --- | --- | --- |
| 1 | Frota | idle de logistica | ⭐⭐ (recomendado) |
| 2 | Cozinha Infinita | idle + merge | ⭐⭐⭐ |
| 3 | Deep Miner | idle de profundidade | ⭐⭐ |
| 4 | Torre de Cartas | roguelite deckbuilder | ⭐⭐⭐⭐ |
| 5 | Jardim de Automatos | factory / puzzle | ⭐⭐⭐⭐ |
| 6 | Um Botao | incremental minimalista | ⭐ (fazer primeiro) |

Ordem: **6 → 1**. O Um Botao e o laboratorio do motor; a Frota reaproveita o motor inteiro.

### Stack
Flutter 3.44.9 / Dart 3.12 (ver [[flutter]]). Mesmas quatro camadas dos outros projetos -
`domain · application · infrastructure · presentation` + `di` e `utils`, com o **motor do jogo
morando em `domain`**, sem Flutter e sem pacote, para dar teste de balanceamento com
`dart test` puro.

`flutter_bloc` (Cubit) + `get_it` + `hive_ce`. Lint `very_good_analysis` + `import_lint.yaml`.
Sem engine - ver [[flutter-puro-sem-engine-nos-jogos]].

### Como rodar / testar
Ainda nao aplicavel - nao ha projeto Flutter criado. Quando houver:
```bash
flutter run
flutter analyze
flutter test          # inclui test/domain/economia_test.dart, que simula 10.000 ticks
```

### Seguranca
Single-player, offline, sem conta e sem servidor: **nao ha superficie de ataque**, e nao ha
segredo para embutir no APK. As 28 regras nao se aplicam hoje.
**Gatilho**: se um dia entrar ranking online, a pontuacao passa a ser calculada e validada no
servidor, o save do aparelho vira palpite do cliente, e valem [[14-validacao-dos-inputs]],
[[08-bloquear-mass-assignment]] e [[04-ativar-rls]].

## Repositorios
Desde 2026-09-11 cada ideia da linha Progressivos tem repositorio privado na organizacao
**RabiscoGames**: `frota`, `cozinha-infinita`, `deep-miner`, `torre-de-cartas`,
`jardim-de-automatos`, `um-botao` - por enquanto so documentos (`docs/roteiro.md` e
`docs/progressivos/`). Ver [[um-repo-por-jogo-com-docs-da-linha]].

## Relacionado
- [[RagdollGames]]
- [[motor-idle-em-flutter]]
- [[flutter-puro-sem-engine-nos-jogos]]
- [[flutter]]
- [[Heavy]]
- [[ICook]]
