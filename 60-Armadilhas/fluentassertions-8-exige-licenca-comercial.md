---
tipo: armadilha
titulo: FluentAssertions 8 exige licenca comercial paga (ate a 7.x era Apache-2.0)
projeto: [BTech.NFe.Api, todos]
stack: [dotnet, nuget]
tags: [tipo/armadilha, stack/dotnet, licenca]
palavras-chave: [fluentassertions, fluent assertions 8, xceed, licenca, community license, non-commercial, dependabot, awesomeassertions, 7.2.0, apache-2.0]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# FluentAssertions 8 exige licenca comercial paga (ate a 7.x era Apache-2.0)
## Resumo
A partir da 8.0 o FluentAssertions esta sob a "Xceed Community License — for Non-Commercial Use"; empresa que fatura precisa de licenca comercial paga. O Dependabot propoe o bump como se fosse uma versao qualquer.

## Contexto
[[BTech.NFe.Api]] usa FluentAssertions 6.12.2 nos testes. O Dependabot abriu o #9 (6.12.0 → 8.10.0)
em 2026-09-07; foi fechado pelo proprio Dependabot em 2026-09-11 e **vai reabrir** num proximo ciclo.

## Detalhe
Conferido no pacote (`api.nuget.org/v3-flatcontainer/fluentassertions/<v>/fluentassertions.nuspec`):
| Versao | Licenca |
| --- | --- |
| 6.12.2, 7.2.0 | `Apache-2.0` |
| 8.10.0 | arquivo `LICENSE` — Xceed Community License, "Any use outside of these parameters requires a paid Commercial License" |

Caminhos:
- **escolhido pelo Jonas em 2026-09-11 (BTech.NFe.Api, PR #15):** travar abaixo da 8 com
  `ignore` no `dependabot.yml`:
  ```yaml
  ignore:
    - dependency-name: "FluentAssertions"
      versions: [">= 8.0.0"]
  ```
- migrar para o fork Apache-2.0 **AwesomeAssertions** (API compativel);
- ou comprar a licenca.

**Pendente:** o [[Heavy]] tambem usa FluentAssertions (versao central em `Directory.Packages.props`)
e nao tem a regra.

Regra geral: bump **major** de dependencia pede olhar a licenca, nao so o changelog.

## Relacionado
- [[BTech.NFe.Api]]
- [[dependabot-mergeado-com-ci-vermelho-quebra-a-main]]
