---
tipo: armadilha
titulo: .NET 10 - OpenAPI gera 3.1 e numeros como integer|string, quebrando o cliente TS
projeto: [Eden]
stack: [dotnet, openapi, typescript]
tags: [tipo/armadilha, stack/dotnet, stack/openapi]
palavras-chave: [AddOpenApi, OpenApiVersion, SchemaTransformer, openapi-typescript, string | number, contract test, NU1903 treatwarningsaserrors]
origem: claude-code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: alta
---

## Resumo
Ao migrar net9 -> net10, o contrato OpenAPI muda (3.1, `type: [integer,string]` + pattern) e o tsc do front quebra; conserte no gerador, nao no front.

## Contexto
Eden: teste de contrato falhou apos trocar TargetFramework; regenerar o schema deu `string | number` e erros TS2345/TS2365.

## Detalhe
- `AddOpenApi(o => { o.OpenApiVersion = OpenApi3_0; o.AddSchemaTransformer(...) })` removendo o flag `String` de tipos Integer/Number e zerando `Pattern`.
- Regenerar: `UPDATE_CONTRACT=1 dotnet test tests/Eden.Api.Tests --filter OpenApiContract` e `npm run gen`.
- Pacotes 10.0.0 vinham com CVE (DataProtection, System.Security.Cryptography.Xml): subir para 10.0.12; Testcontainers 4.1 puxa SSH.NET vulneravel -> 4.15.
- Nao suprimir com NoWarn.

## Relacionado
[[dotnet-10-como-padrao-de-backend]] · [[suprimir-aviso-de-vulnerabilidade-com-nowarn]] · [[2026-10-01-eden-validacao-ambiente]]
