---
tipo: padrao
titulo: Estilo C# reforcado pelo build, nao pelo revisor
projeto: [Heavy]
stack: [dotnet, csharp]
tags: [tipo/padrao, stack/dotnet, cerebro/padrao-obrigatorio]
palavras-chave: [estilo, editorconfig, treatwarningsaserrors, code style, csharp, sealed, record, primary constructor, cancellationtoken, var]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Estilo C# reforcado pelo build, nao pelo revisor
## Resumo
`TreatWarningsAsErrors` + `EnforceCodeStyleInBuild` + `.editorconfig` com regras como `error`: cada desvio de estilo quebra a compilacao.

## Contexto
Configurado em `backend/Directory.Build.props` do [[Heavy]]. A ideia e tirar estilo da revisao
humana: se compilou, o estilo esta certo.

## Detalhe

### Regras que quebram o build
- `namespace` file-scoped (`namespace X;`), `using` **fora** do namespace
- `var` sempre — inclusive em tipo primitivo
- **primary constructor** em classe com dependencia injetada
- `sealed` por padrao (`CA1852`); so nao sela o que e herdado de proposito
- `record` para DTO; classe so quando houver comportamento
- zero `using` desnecessario (`IDE0005`)
- colecao com sintaxe nova (`IDE0300`)
- campo privado `_camelCase`, interface comecando com `I`
- `switch` como expressao quando couber

### Alem do que o compilador ve
- **`CancellationToken` em todo metodo `async`**, propagado ate o fim, com sufixo `Async` no nome
- `HeavyOps.Domain` nao pode ganhar `PackageReference`

### Arquivos que nao se altera de passagem
`Directory.Packages.props` · `Directory.Build.props` · `.editorconfig` · `.slnx` · `.csproj`
Precisa de um pacote novo? **Diga qual e por que**, em vez de adicionar.

### Equivalentes nas outras superficies
- Painel: `npm --prefix painel run lint` (ESLint com `boundaries`, `jsx-a11y`)
- App: `cd app && flutter analyze` (`very_good_analysis` + `import_lint.yaml`)

Ambos reprovam import que atravessa camada.

## Relacionado
- [[Heavy]]
- [[arquitetura-dotnet-em-camadas]]
