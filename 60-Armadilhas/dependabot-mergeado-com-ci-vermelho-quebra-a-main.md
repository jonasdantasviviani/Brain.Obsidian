---
tipo: armadilha
titulo: PRs do Dependabot mergeados com CI vermelho quebraram a main (Hangfire e Swashbuckle 10)
projeto: [BTech.NFe.Api]
stack: [dotnet, nuget, github-actions, hangfire, swashbuckle]
tags: [tipo/armadilha, stack/dotnet, stack/nuget]
palavras-chave: [dependabot, grupo de atualizacao, NU1107, NU1608, hangfire.core, hangfire.aspnetcore, hangfire.sqlserver, swashbuckle 10, microsoft.openapi 2, Microsoft.OpenApi.Models, OpenApiSecuritySchemeReference, AddSecurityRequirement, ci vermelho, restore falha, main quebrada]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# PRs do Dependabot mergeados com CI vermelho quebraram a main (Hangfire e Swashbuckle 10)
## Resumo
Em 2026-09-11 a main da BTech.NFe.Api parou no `dotnet restore` (#8) e deixou de compilar (#12); como o CI ja estava vermelho desde o #4, ninguem percebeu a diferenca entre "vermelho de sempre" e "quebrado de verdade".

## Contexto
O CI da [[BTech.NFe.Api]] falhava desde o PR #4 por causa de um teste de integracao que exige SQL
Server. Com tudo vermelho, os PRs #6, #7, #8, #11, #12 e #13 foram mergeados assim mesmo.

## Detalhe

### 1. Grupo de atualizacao sobe um pacote e esquece o irmao
#8 (grupo `dotnet-minor-e-patch`) subiu `Hangfire.SqlServer` 1.8.24→1.8.25 mas nao
`Hangfire.AspNetCore` → duas versoes exatas de `Hangfire.Core` exigidas:
`NU1107 Version conflict detected for Hangfire.Core` / `NU1608 ... outside of dependency constraint`
(erro por `TreatWarningsAsErrors`). Correcao: alinhar todos os `Hangfire.*` na mesma versao.
Prevencao (aplicada no PR #15): grupo no `dependabot.yml`, **antes** do grupo generico — o
Dependabot coloca cada dependencia no primeiro grupo que casa:
```yaml
groups:
  hangfire:
    patterns: ["Hangfire*"]      # sem update-types: cobre major tambem
  dotnet-minor-e-patch:
    update-types: ["minor", "patch"]
```
Validar `dependabot.yml` localmente: converter para JSON (`ruby -ryaml -rjson`) e checar com
`jsonschema` contra `https://www.schemastore.org/dependabot-2.0.json` (o `json.schemastore.org`
redireciona — usar `curl -L`). Fazer um teste negativo para provar que o validador reprova algo.

Estado em 2026-09-11 depois do #14: main verde de novo (Build & Test + Docker Build).

### PRs duplicados de major por projeto (2026-09-11 a tarde)
Com `directory: "/"` e grupo so para minor/patch, cada **major** virou um PR **por projeto de teste**
(Test.Sdk 17→18 em 4 PRs, xunit.runner 2→4 em 4, FluentAssertions 6→7 em 3), alem de PRs "multi"
agrupados da mesma atualizacao. Mergear os por projeto deixa os agrupados em conflito (#17/#18/#19,
fechados como substituidos). Antes de "resolver" conflito de PR do Dependabot, conferir se a mudanca
ja nao entrou por outro PR — resolver so reaplicaria. Prevencao proposta: grupo `testes`
(`FluentAssertions`, `xunit*`, `Microsoft.NET.Test.Sdk`, `NSubstitute*`) sem `update-types`.

### O mesmo no BTech.Web
[[BTech.Web]]: #6 (TypeScript 5.9 → 7.0.2) e #7 (ESLint 9 → 10) mergeados com CI vermelho quebraram
o lint — `typescript-eslint does not support TS 7.0` e `context.getFilename is not a function`
(eslint-plugin-react). Consertar uma quebra revelou a proxima: so depois de voltar o TS o lint
chegou no erro do ESLint 10, e so depois dele apareceram 71 erros de codigo que nunca tinham rodado.
Corrigido no Web #11: versoes de volta + `ignore` no Dependabot pelos limites de peer dos plugins
(`npm view`/`package.json` do plugin → `peerDependencies`). Licao: antes de liberar um major, checar
o `peerDependencies` de quem consome o pacote (aqui, os plugins de lint), nao so o pacote.

### 2. Swashbuckle 10 = Microsoft.OpenApi 2 (breaking)
```csharp
using Microsoft.OpenApi;                       // era Microsoft.OpenApi.Models
options.AddSecurityRequirement(document => new OpenApiSecurityRequirement
{
    [new OpenApiSecuritySchemeReference("Bearer", document)] = [],
});                                             // era OpenApiSecurityScheme { Reference = ... }
```
`AddSecurityDefinition`, `OpenApiInfo`, `SecuritySchemeType`, `ParameterLocation` so mudam de namespace.

### 3. CI vermelho cronico esconde quebra nova
Teste que exige recurso ausente no runner deve sair do CI por trait
(`[Trait("Requer", "SqlServer")]` + `--filter "Requer!=SqlServer"`), nunca ficar falhando.
Correcao feita no PR #14 (`fix/ci-main`), verde no GitHub.

### Diagnostico rapido
```bash
gh api --allow-escape-sequences repos/<org>/<repo>/actions/jobs/<job>/logs > job.log
```
(sem `--allow-escape-sequences` o `gh` recusa gravar o log.)

## Relacionado
- [[BTech.NFe.Api]]
- [[fluentassertions-8-exige-licenca-comercial]]
- [[suprimir-aviso-de-vulnerabilidade-com-nowarn]]
