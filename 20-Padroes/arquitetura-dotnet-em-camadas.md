---
tipo: padrao
titulo: Arquitetura .NET em camadas (uma pasta por camada)
projeto: [Heavy, BTech.NFe.Api, ICook]
stack: [dotnet, csharp]
tags: [tipo/padrao, stack/dotnet, cerebro/padrao-obrigatorio]
palavras-chave: [arquitetura, camadas, clean architecture, webapi, application, domain, infrastructure, utils, dotnet, estrutura de projeto, slnx]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Arquitetura .NET em camadas (uma pasta por camada)
## Resumo
Todo back-end .NET usa `src/<Camada>/<Projeto>.<Camada>/`, com dependencias apontando para dentro e `Domain` sem nenhum pacote.

## Contexto
Padrao obrigatorio, formalizado como skill `backend-architecture` em `Heavy/.claude/skills/`.
Ja aplicado em [[Heavy]], [[BTech.NFe.Api]] e [[ICook]]. A skill e generica e reutilizavel entre
produtos: regra de negocio de um produto especifico vive em `references/`, nunca na skill.

## Detalhe

### Layout
```text
Dockerfile
azure-pipelines.yml
Directory.Build.props        TargetFramework, Nullable, warnings-as-errors
Directory.Packages.props     Central Package Management
.editorconfig
NomeDoProjeto.slnx           formato .slnx (XML), nao .sln
│
├── src/
│   ├── WebApi/          NomeDoProjeto.WebApi          Controllers, BackgroundServices
│   ├── Application/     NomeDoProjeto.Application     casos de uso, services, validators
│   ├── Domain/          NomeDoProjeto.Domain          entidades, enums, interfaces — ZERO pacote
│   ├── Infrastructure/  NomeDoProjeto.Infrastructure  + .Sql .Redis .Storage .Maps .Messaging
│   └── Utils/           NomeDoProjeto.Utils           conversores, validadores
└── tests/               UnitTest · ContractTests · IntegratedTests · FunctionalTests
```

### As regras
1. **Uma pasta por camada** dentro de `src/`, com o projeto dentro dela. Mantem a raiz limpa e
   deixa as infras adicionais crescerem sem poluir.
2. **Dependencias apontam para dentro.** `Domain` nao conhece ninguem.
3. **`Domain` nao pode ganhar `PackageReference`.** Se precisou de um, a modelagem esta errada.
4. **Application nao referencia Infrastructure.** Contratos (interfaces de servico e DTOs) moram
   no `Domain`; a composicao acontece no `WebApi`.
5. Se um arquivo nao se encaixa claramente numa camada, **a modelagem esta errada** — resolva
   isso antes de criar a pasta.
6. **Quatro projetos de teste**, sempre: unit, contract, integrated, functional.

### O mesmo padrao nas outras superficies
As quatro camadas se repetem no painel React e no app Flutter com os **mesmos nomes**
(`domain · application · infrastructure · presentation` + `utils`). Lint reprova import que
atravessa camada: ESLint com `boundaries` no painel, `import_lint.yaml` +
`very_good_analysis` no Flutter.

### Historico de aplicacao
No [[BTech.NFe.Api]] a migracao foi feita em quatro commits encadeados:
composition root + service layer nos controllers → remover dependencia Application→Infrastructure.Sql
→ mover interfaces de servico e DTOs para o Domain → uma pasta por camada sob `src/`.

## Relacionado
- [[Heavy]]
- [[BTech.NFe.Api]]
- [[ICook]]
- [[estilo-csharp-reforcado-pelo-build]]
- [[skills-por-pasta-no-claude]]
