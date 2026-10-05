---
tipo: projeto
titulo: Heavy Ops
projeto: [Heavy]
stack: [dotnet, csharp, postgresql, react, flutter, redis, azure]
tags: [tipo/projeto, stack/dotnet, stack/flutter, stack/react, dominio/logistica]
palavras-chave: [heavy, heavy ops, transportadora, logistica, multi-inquilino, multitenant, rls, rastreamento, motorista, entrega, ping, postgres, flutter, saas]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Heavy Ops
## Resumo
SaaS multi-inquilino para transportadoras: prova de entrega com visibilidade, app offline-first do motorista e link publico de rastreio.

## Contexto
`~/Documents/Repos/Pessoal/Heavy` · `github.com/jonasdantasviviani/Heavy.git` · branch `main`.
Nasceu como projeto "Transporte" (ver [[2026-08-07-transporte-concepcao-do-produto]]).
A especificacao (docs `00` a `28` na raiz) e a **fonte da verdade**: quando codigo e doc
discordam, o doc esta certo ate alguem decidir por escrito.

## Detalhe

### Escopo da V1
Prova de entrega com visibilidade: app do motorista offline-first, ping de localizacao,
link publico de rastreio para o cliente final, painel do dia para o dono.
**Fora da V1 de proposito:** fiscal, roteirizacao, custo/km.

Escala de referencia de um cliente grande: **~40 mil entregas/dia**.

### Monorepo
```text
00-*.md … 28-*.md   especificacao (fonte da verdade)
backend/            .NET 10 / C# 14 — API, Razor do link publico, workers · HeavyOps.slnx
  src/WebApi | Application | Domain | Infrastructure(+.Sql .Redis .Storage .Maps .Messaging) | Utils
  tests/  UnitTest · ContractTests · IntegratedTests · FunctionalTests
painel/             React 19 + TypeScript + Vite
app/                Flutter — "Heavy Drive", so Android na V1
infra/              postgres 16 · redis 7 · azurite
.claude/skills/     padroes obrigatorios de arquitetura e design system
```
As quatro camadas se repetem nas tres superficies com os **mesmos nomes** e a mesma regra:
dependencias apontam para dentro, `domain` nao conhece ninguem.

### Stack decidida
| Superficie | Escolha | Motivo |
| --- | --- | --- |
| Back-end | .NET 10 / C# 14 | ingestao de ping rapida, workers no mesmo processo, Npgsql |
| Banco | PostgreSQL 16 | RLS resolve multi-inquilino; particionamento resolve ping |
| Painel | React + TypeScript + Vite | mapa e tempo real dominam a tela |
| Link publico | Razor dentro da propria API | HTML leve em 2s no 3G, sem projeto extra |
| App motorista | Flutter, so Android na V1 | tela igual em Android 8, APK 8-12 MB |
| Nuvem | Azure + Azure DevOps | ferramental integrado com .NET |

### Versoes verificadas na maquina
.NET SDK 10.0.302 · Node 26.6.0 (≥20.19) · Flutter 3.44.9 (Dart 3.12) · Docker 29.6.2 (compose 5.4.0)

### Subir o ambiente
```bash
docker compose -f infra/docker-compose.yml up -d   # postgres 5433 · redis 6380 · azurite 10000
```

### Skill obrigatoria por pasta
| Pasta | Skill |
| --- | --- |
| `backend/` | `backend-architecture` (+ `references/projeto-heavyops.md`) |
| `painel/` arquitetura | `frontend-architecture` |
| `painel/` visual | `design-system-frontend` |
| `app/` arquitetura | `mobile-architecture` |
| `app/` visual | `design-system-mobile` |
| `infra/` | nenhuma; seguir doc 22 e `infra/README.md` |

Ver [[skills-por-pasta-no-claude]].

### Regras criticas
- Multi-inquilino: [[multi-inquilino-com-rls-no-postgres]] — as 4 regras. Violar vaza dado entre transportadoras em silencio.
- Contrato: [[openapi-gerado-do-codigo]] — tres documentos (`integracao`, `app`, `painel`).
- Estilo: o build reprova, nao o revisor. Ver [[estilo-csharp-reforcado-pelo-build]].
- Idioma: [[dominio-em-portugues-tecnico-em-ingles]].

### Nunca commitar
`appsettings.local.json`, `.env`, keystore Android (`*.jks`, `*.keystore`, `key.properties`),
`*.pem`, `*.pfx`, `secrets.json`, `node_modules/`, `bin/`, `obj/`, cliente gerado do OpenAPI.
Segredo de producao vive no Key Vault. Credenciais do Azurite no compose sao a **unica excecao**
(publicas e documentadas pela Microsoft). Se precisou de `git add -f`, pare e pergunte.

## Relacionado
- [[multi-inquilino-com-rls-no-postgres]]
- [[monolito-modular-em-vez-de-microservicos]]
- [[eventos-como-fonte-da-verdade]]
- [[separar-operacional-de-analitico]]
- [[rls-com-pool-de-conexoes-npgsql]]
- [[arquitetura-dotnet-em-camadas]]
