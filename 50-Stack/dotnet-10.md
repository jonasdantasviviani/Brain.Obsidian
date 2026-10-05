---
tipo: stack
titulo: .NET 10 / C# 14
projeto: [Heavy, BTech.NFe.Api, ICook]
stack: [dotnet, csharp]
tags: [tipo/stack, stack/dotnet]
palavras-chave: [dotnet, net10, csharp, c# 14, aspnet, sdk, slnx, backgroundservice, ef core 10, hasdefaultvalue, sentinel, bool default, interceptor, sql gerado sem banco]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-28
confianca: alta
---

# .NET 10 / C# 14
## Resumo
Runtime e linguagem padrao de todo back-end; SDK 10.0.302 verificado nesta maquina.

## Detalhe

- Target: `net10.0`, definido em `Directory.Build.props`
- Pacotes via **Central Package Management** (`Directory.Packages.props`)
- Solucao em **`.slnx`** (XML), nao `.sln` — `dotnet build Projeto.slnx` funciona igual
- `BackgroundService` roda no mesmo processo da API: um servico a menos para operar
- Conferir versao: `dotnet --version`

### Recursos de linguagem esperados no codigo
`namespace` file-scoped · primary constructor · `record` para DTO · `sealed` por padrao ·
`switch` como expressao · colecoes com sintaxe nova. Ver [[estilo-csharp-reforcado-pelo-build]].

### EF Core 10: `bool` com `HasDefaultValue(true)` grava `false` normalmente
Verificado em 2026-09-28 (EF Core SqlServer 10.0.12, entidade `EmpresaEmissao` do [[BTech.NFe.Api]]):
o EF 10 poe o **sentinel = valor default** (`Sentinel=True`). Um `false` entra no INSERT;
so o `true` e omitido para o banco aplicar o DEFAULT. A armadilha antiga (EF 7 e antes:
`false` = default do CLR, coluna omitida, banco grava `1`) nao vale mais.

Como conferir sem banco nenhum: `DbConnectionInterceptor` que devolve
`InterceptionResult.Suppress()` em `ConnectionOpening(Async)`/`ConnectionClosing(Async)` +
`DbCommandInterceptor` que guarda `CommandText` em `ReaderExecuting(Async)` e lanca excecao.
`UseSqlServer("Server=nao-existe;...")` basta; o SQL gerado aparece sem abrir conexao.
Metadados: `ctx.Model.FindEntityType(typeof(T))!.FindProperty("X")!.Sentinel` / `.ValueGenerated`.

### Onde e usado
[[Heavy]] (.NET 10) · [[BTech.NFe.Api]] (.NET 10, migrado de 8) · [[ICook]] (ASP.NET Core 9, migrando)

## Relacionado
- [[dotnet-10-como-padrao-de-backend]]
- [[arquitetura-dotnet-em-camadas]]
