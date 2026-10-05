---
tipo: decisao
titulo: Backend Go descartado no iCook (ADR 0001)
projeto: [ICook]
stack: [dotnet, csharp, go]
tags: [tipo/decisao, stack/dotnet]
palavras-chave: [go, fiber, csharp, aspnet, migracao, adr, icook, backend, descartado]
origem: claude-code
criado: 2026-01-01
atualizado: 2026-09-07
confianca: alta
---

# Backend Go descartado no iCook (ADR 0001)
## Resumo
O backend Go/Fiber do iCook foi descartado em favor de C# / ASP.NET Core; o codigo Go legado nao deve ser estendido.

## Contexto
ADR 0001 do [[ICook]], registrado no commit
`56d9823 feat: 01-00 bootstrap C# — ICook.sln (Clean Architecture), Serilog, /health, Docker .NET, CI Actions; backend Go descartado (ADR 0001)`.

## Detalhe

### A regra operacional
- **Nao adicionar features novas ao backend Go.**
- Qualquer trabalho de backend a partir do ADR e na estrutura C# (`ICook.slnx`), mesmo que ainda
  nao exista fisicamente no repo.
- A pasta `backend/` ainda contem o Go legado — e para **descartar, nao estender**.
- O Flutter em `mobile/` e mantido: nao descartar nem reescrever telas existentes sem necessidade.

### Por que
Alinhamento com o padrao de back-end de todos os outros projetos
([[dotnet-10-como-padrao-de-backend]]) — uma stack a menos para manter, e reuso direto da
[[arquitetura-dotnet-em-camadas]].

## Relacionado
- [[ICook]]
- [[dotnet-10-como-padrao-de-backend]]
