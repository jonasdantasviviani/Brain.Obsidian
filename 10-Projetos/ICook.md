---
tipo: projeto
titulo: iCook
projeto: [ICook]
stack: [flutter, dart, dotnet, csharp, postgresql, redis]
tags: [tipo/projeto, stack/flutter, stack/dotnet, dominio/mobile]
palavras-chave: [icook, receitas, despensa, ingredientes, flutter, mobile, afiliado, aspnet, supabase, railway, anthropic, freemium]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# iCook
## Resumo
App mobile Flutter + API C# que resolve "tenho ingredientes em casa mas nao sei o que cozinhar".

## Contexto
`~/Documents/Repos/Pessoal/ICook` · `github.com/jonasdantasviviani/ICook.git` · branch `main`.

**Core value:** o usuario abre o app, ve o que pode cozinhar agora com o que tem, e compra o que
falta em 1 clique.

## Detalhe

### Modelo de negocio (triplo desde o MVP)
Afiliados (Mercado Livre / iFood) · freemium + IAP · receitas patrocinadas B2B.

### Stack
| Camada | Tecnologia |
| --- | --- |
| Mobile | Flutter 3.x (Dart), iOS + Android |
| Backend | C# / ASP.NET Core 9 (Minimal API) |
| ORM/DB | EF Core 9 + Npgsql (raw SQL para o match de ingredientes) |
| Banco | PostgreSQL 15 (Supabase) |
| Cache | Redis (Upstash) |
| IA | Anthropic Claude API |
| Deploy | Railway (API) + Vercel (landing) |

### Estrutura de documentacao (nao duplicar entre elas)
| Camada | Local | Responsabilidade |
| --- | --- | --- |
| Planejamento de produto | `.planning/` | o que construir, em que ordem, por que |
| Constituicao para IA | `docs/ai/` | como pensar e trabalhar neste codigo (regras, nao descricoes) |
| Decisoes pontuais | `docs/decisions/` | ADRs |

Leitura obrigatoria antes de tarefa nao-trivial: `.planning/STATE.md` → `.planning/PROJECT.md` →
`docs/ai/CURRENT_STATE.md` → `docs/ai/HOW_AI_SHOULD_THINK.md`.

### Estado
Backend em **migracao de Go/Fiber para C# / ASP.NET Core 9** — o Go em `backend/` e legado a
descartar, nao estender (ADR 0001, ver [[backend-go-descartado-no-icook]]).
Flutter em `mobile/` e mantido: nao reescrever telas existentes sem necessidade.
Phase 0 (validacao pre-codigo) do `ROADMAP.md` ainda nao concluida.

### Rodar local
```bash
docker-compose -f infra/docker-compose.yml up -d   # postgres + redis
cp backend/.env.example backend/.env
cd backend/ICook.API && dotnet ef database update && dotnet run   # :8080
```
Aqui **schema muda por EF Core migration** — ao contrario da [[BTech.NFe.Api]], que usa scripts SQL.

### Criterios de qualidade do repo
- Nenhum log via `Console.WriteLine`/`print` — sempre `ILogger`/Serilog estruturado
- Nenhuma secret commitada, apenas `.env.example`
- Regras de `docs/ai/BUSINESS_RULES.md` sao inegociaveis salvo ADR explicito
- Antes de dizer "pronto", rodar o checklist "Looks Done But Isn't" de `.planning/research/PITFALLS.md`

### Divisao de agentes
**Fable** entende, pesquisa, desenha arquitetura, decompoe, revisa, cria RFCs.
**Claude Code** implementa, testa, refatora, documenta. Ver [[fable-planeja-claude-code-implementa]].

## Relacionado
- [[backend-go-descartado-no-icook]]
- [[fable-planeja-claude-code-implementa]]
- [[arquitetura-dotnet-em-camadas]]
