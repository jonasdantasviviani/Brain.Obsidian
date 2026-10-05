---
tipo: decisao
titulo: Backend padrao: .NET 10 / C# 14
projeto: [Heavy, BTech.NFe.Api, ICook]
stack: [dotnet, csharp]
tags: [tipo/decisao, stack/dotnet]
palavras-chave: [dotnet, net10, csharp, backend, decisao, stack, linguagem, runtime]
origem: claude-code
criado: 2026-01-01
atualizado: 2026-09-07
confianca: alta
---

# Backend padrao: .NET 10 / C# 14
## Resumo
.NET 10 / C# 14 e o back-end padrao de todos os projetos; decisao tomada e nao revisitar por 12 meses.

## Contexto
Formalizado no [[Heavy]] (doc 22, §2) e aplicado tambem na [[BTech.NFe.Api]] (migrada de .NET 8
para .NET 10) e no [[ICook]] (que migrou de Go/Fiber para ASP.NET Core).

## Detalhe

### Por que
- Runtime rapido o bastante para ingestao de ping em escala (~40 mil entregas/dia de um cliente)
- `BackgroundService` roda no mesmo processo: **um servico a menos para operar**
- Tipagem forte no dominio
- Npgsql e um dos melhores drivers de Postgres de qualquer ecossistema

### Regra que governa a escolha
> **Escolha a tecnologia mais chata que resolve o problema.**

Com um desenvolvedor, cada peca nova de infraestrutura e uma peca a mais para operar, monitorar,
atualizar e depurar as 3h da manha. O gargalo do projeto nao e performance — e **tempo de quem
escreve o codigo**. Corolario: *um banco, um servico, uma fila. Nada mais ate doer.*

### Alternativas descartadas
- **Go/Fiber** — era o backend do [[ICook]], descartado no ADR 0001. Ver [[backend-go-descartado-no-icook]].
- Node/Python no back-end: nao entregam a ingestao de ping com a mesma folga nem os workers in-process.

### Formato de solucao
Todos usam **`.slnx`** (XML), nao `.sln`. Ver [[arquitetura-dotnet-em-camadas]].

## Relacionado
- [[tecnologia-mais-chata-que-resolve]]
- [[arquitetura-dotnet-em-camadas]]
- [[dotnet-10]]
