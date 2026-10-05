---
tipo: armadilha
titulo: import_lint 2.0 - config so no analysis_options, um caminho por regra, e regra errada falha em silencio
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/armadilha, stack/flutter, stack/dart]
palavras-chave: [import_lint, diagnostics, LintCode, dart analyze, flutter analyze, info, unrecognized_error_code, registerWarningRule, import_lint.yaml, analysis_options, regra de camadas, arquitetura, target, from, except, glob, "import_lint is required", "except must be a List", plugin, analysis_server_plugin, severity, sonda, lint silencioso]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# import_lint 2.0 - config e regra silenciosa
## Resumo
A regra de camadas dos jogos Ragdoll **nunca rodou** desde 2026-09-09: o `import_lint 2.0` ignora
`import_lint.yaml`, exige `except`, aceita um caminho so por `target`/`from` - e varios erros de
config nao dao erro nenhum, a regra simplesmente nao casa nada.

## Contexto
`need-for-ragdoll` e `ragdoll-go`, 2026-09-12. O documento `01-estrutura-do-projeto` da serie
ensinava um `import_lint.yaml` solto, com listas separadas por virgula e `package:*/`. Lido o
pacote em `~/.pub-cache/hosted/pub.dev/import_lint-2.0.0/lib/src/config/`.

## Detalhe

### Os jeitos de a regra nao valer nada
| Config | O que acontece |
| --- | --- |
| `import_lint.yaml` solto na raiz | `dart run import_lint` morre: `import_lint is required` |
| regra sem `except` | morre: `except must be a List<String>` |
| `from: "package:flutter/**, package:flame/**"` | **silencioso** - pega so o primeiro pacote e o resto vira glob literal |
| `package:*/domain/**` | **silencioso** - o nome do pacote vira `*` e nao casa nada |
| config copiada de outro jogo com o nome do pacote dele | **silencioso** - `package:need_for_ragdoll/` dentro do `ragdoll-go` |

### O formato que funciona
Dentro do `analysis_options.yaml`, um caminho por campo, `except` sempre:
```yaml
include: package:very_good_analysis/analysis_options.yaml

import_lint:
  rules:
    domain_puro_sem_flutter:
      target: "package:ragdoll_go/domain/**"
      from: "package:flutter/**"
      except: []
    forge2d_so_na_infra_presentation:
      target: "package:ragdoll_go/presentation/**"
      from: "package:flame_forge2d/**"
      except: []
```
Regra do documento com lista vira **familia com o mesmo prefixo** - nos jogos, 3 regras viraram
10 (`domain_puro_sem_{flutter,flame,forge2d,application,infrastructure,presentation}`,
`application_nao_ve_ui_{flutter,presentation}`, `forge2d_so_na_infra_{application,presentation}`).

### Como o caminho e casado (constraint_resolver / resource_locator)
- **alvo**: caminho do arquivo relativo a `lib/`, pacote = `name` do `pubspec.yaml`.
- **import** `package:x/y/z.dart` = pacote `x`, caminho `y/z.dart`; import relativo e resolvido;
  `dart:math` vira pacote `dart`.
- violacao = alvo casa **e** `from` casa **e** nenhum `except` casa. `except` casa o **import**,
  nao o arquivo - nao serve para isentar um arquivo-alvo.

### Provar que a regra casa: sonda
"No issues found" nao prova nada, porque config errada tambem da isso. Criar arquivo com import
proibido em cada camada, rodar, conferir a regra acusada, apagar:
```bash
printf "import 'package:flutter/foundation.dart';\n" > lib/domain/sonda_import_lint.dart
dart run import_lint        # tem de acusar domain_puro_sem_flutter
rm lib/domain/sonda_import_lint.dart
```

### Quem reprova - e o que o plugin faz de verdade (testado com sonda)
| Onde | O que aparece |
| --- | --- |
| `dart run import_lint` com `severity: "error"` | `error` e **sai com 1** - e o portao |
| `dart run import_lint` sem `severity` | `warning` e sai com **0** mesmo violando |
| plugin `plugins: import_lint: ^2.0.0` | **nada** - carrega, compila, e a regra e descartada |
| plugin com `diagnostics: import_lint: true` | `info` no `dart analyze` e na IDE - nao reprova |
| `flutter analyze` (Flutter 3.44.9) | **nada**, com ou sem `diagnostics` |
| `analyzer: errors: import_lint: error` (ou `import_lint/import_lint`) | `unrecognized_error_code` - nao sobe a severidade |

Por que precisa do `diagnostics`: o plugin registra a regra com `registerWarningRule` (que a doc
do `analysis_server_plugin` diz vir ligada), mas o codigo dela e um `LintCode`, e o servidor a trata
como lint desligado.
```yaml
plugins:
  import_lint:
    version: ^2.0.0
    diagnostics:
      import_lint: true
```

**Para saber se o plugin roda de fato:** quebrar a config de proposito (tirar um `except`). O
`dart analyze` derruba o servidor de analise com a stack do `import_lint`
(`PluginServer._computeDiagnosticsFromPlugin`). Restaurar do backup e conferir com `cmp`.

- O `import_lint.yaml` tambem aparece em notas do Heavy - la **nao conferido** qual versao roda.

## Relacionado
- [[RagdollGames]]
- [[flutter]]
- [[forge2d-e-box2d-v3]] - mesma licao: ler a fonte em `~/.pub-cache` antes
- [[arquitetura-dotnet-em-camadas]]
- [[jogo-flame-estende-base-de-fisica-na-infra]]
- [[bash-paralelo-compartilha-diretorio]]
