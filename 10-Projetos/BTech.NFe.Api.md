---
tipo: projeto
titulo: BTech.NFe.Api
projeto: [BTech.NFe.Api]
stack: [dotnet, csharp, sqlserver, docker, efcore]
tags: [tipo/projeto, stack/dotnet, empresa/btech, dominio/fiscal]
palavras-chave: [nfe, nota fiscal, btech, api, dotnet, aspnet, sqlserver, focus nfe, jwt, docker, efcore, swagger, banco em branco, administrador, importador, bak, tenant]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-29
confianca: alta
---

# BTech.NFe.Api
## Resumo
API REST em ASP.NET Core 10 para o sistema de Nota Fiscal Eletronica da BTech, com SQL Server, JWT e Docker.

## Contexto
`~/Documents/Repos/BTech/BTech.NFe.Api` · `github.com/B-Tech-Sistemas/BTech.NFe.Api` · branch `main`.
PRs entram por **squash** (ex.: #4) — nao reaproveitar branch ja mergeada, ver
[[branch-mergeada-por-squash-e-apagada-no-remoto]].
Solucao no formato **`.slnx`** (`BTech.NFe.slnx`), nao `.sln`.

## Detalhe

### Stack
- ASP.NET Core 10, C# 14, `net10.0`
- EF Core + **SQL Server 2022 Express** (via Docker)
- AutoMapper (`MappingProfile`), FluentValidation (`AddFluentValidationAutoValidation`)
- Serilog estruturado · JWT Bearer (`Jwt:Key/Issuer/Audience`) · BCrypt (`PasswordService`)
- Politica de autorizacao `SuperUsuario` exige claim `super_usuario = "S"`
- Rate limiting no login: 5 req/min por IP, desligado em `Testing`
- Integracao **Focus NFe** via `HttpClient("FocusNfe")` nomeado, com Polly (retry + circuit breaker)
- OutputCache com policy `ReferenceData` · Health check em `/health` · CORS de `Cors:Origins`
- `GlobalExceptionHandler` + `ProblemDetails` · filtros globais `PaginationHeaderFilter` e `PaginationValidationFilter`

### Estrutura (uma pasta por camada — ver [[arquitetura-dotnet-em-camadas]])
```text
src/WebApi/BTech.NFe.Api/                 Controllers, Filters, HealthChecks, Middleware, Swagger
src/Application/BTech.NFe.Application/    Services, Validators, Mappings, Jobs, DI
src/Domain/BTech.NFe.Domain/              Entities, Interfaces, Models
src/Infrastructure/BTech.NFe.Infrastructure/
src/Infrastructure/BTech.NFe.Infrastructure.Sql/   DbContext EF Core, Repositories
migrations/    scripts SQL aplicados pelo servico db-migrate do Compose (000 = schema base)
docs/API.md    contrato publico da API (referencia do frontend)
scripts/       setup local (.ps1 Windows, .sh macOS/Linux) — so sobe a stack
tests/         Unit, Contract, Functional, Integration
```
`backups/` na raiz ainda existe no disco do Jonas (gitignored, tem `BTechPLUSTESTE.BAK` de 34 MB
para testar o upload), mas **nao e mais montado** em nenhum container.

### Ambiente local = banco em branco (desde 2026-09-11)
- `docker compose up -d --build` cria o banco com `COLLATE Latin1_General_CI_AS` e
  `AUTO_CLOSE OFF`; `000_schema_base.sql` cria as 143 tabelas do `dbo` sem dados.
- Login local: **`Administrador` / `123456`** (AdminSistema + SuperUsuario, tenant Padrao),
  criado por `015_seed_admin_local.sql` so com `SEED_ADMIN=true` (apenas no override).
- Dados de cliente entram pelo painel: criar tenant (`POST /api/admin/tenants`) → upload
  (`POST /api/importador/backups`) → restaurar em `stg_import_*` → `preview` → `iniciar`
  (Hangfire) → `DELETE .../backups/staging/{db}`. Importa so Cadastros (Empresas, Vendedores,
  GruposProdutos, Fornecedores, Clientes, Produtos).
- `.bak` enviados ficam no volume `btech-backups` (`/backups` na API e no SQL Server).
- Reset: `docker compose down -v`.
- Ver [[banco-em-branco-com-importacao-por-painel]].

### Como rodar e testar
```bash
dotnet build BTech.NFe.slnx
dotnet test tests/BTech.NFe.Tests.Unit/BTech.NFe.Tests.Unit.csproj
dotnet test tests/BTech.NFe.Tests.Contract/BTech.NFe.Tests.Contract.csproj
dotnet test tests/BTech.NFe.Tests.Functional/BTech.NFe.Tests.Functional.csproj
dotnet test tests/BTech.NFe.Tests/BTech.NFe.Tests.csproj
```
Se o build da solucao inteira ficar instavel, compile projeto a projeto.
`ImportadorServiceIntegrationTests` exige SQL Server local com login Windows: tem
`[Trait("Requer", "SqlServer")]`, o CI roda com `--filter "Requer!=SqlServer"` (desde o PR #14).
CI em `.github/workflows/ci.yml`: restore → scan de vulnerabilidades → build Release → Unit →
integracao → Contract → Functional. Ver [[dependabot-mergeado-com-ci-vermelho-quebra-a-main]].
Guia rapido de setup por SO em `INSTALLATION.md`.

### Entidades de chave composta (pegadinha recorrente)
- `CorNota` → `IdNota + Sequencia`
- `CorPedido` → `Idpedido + Sequencia`
- `CorEntrada` → `Identrada + Sequencia`
- `FormulaService` usa `Usuario` como PK, **nao** `CodUsuario`

Services herdam `ServiceBase<TEntity, TKey>`; para chave composta e preciso sobrescrever
`GetByIdAsync` e `DeleteAsync`. Ver [[repositorio-generico-e-servicebase]].

### Cadastro: PUT parcial e chaves sem IDENTITY (desde 2026-09-11)
Todo `PUT` de cadastro carrega o registro pela consulta filtrada por tenant e aplica so os
campos presentes no JSON — ver [[put-parcial-sobre-o-registro-existente]]. Campo ausente nao e
tocado (`PUT /api/usuarios/{id}` sem `senha` mantem a senha), campo `null` apaga.
`Empresas.CodEmpresa` e `CabPedidos.NumeroPedido` **nao** sao IDENTITY: quem gera o proximo
codigo e o service (`IRepository.ProximoCodigoAsync`). Coluna legada NOT NULL com DEFAULT ganha
valor em `<Entidade>.Padroes.cs` — ver
[[nullable-enable-transforma-coluna-legada-em-campo-obrigatorio]].
CNPJ/CPF sao validados por digito verificador (`DocumentoFiscal`) e endereco exige CEP.

### Dominios de servico registrados
Core (Auth, Password, Produto, Cliente, Fornecedore, Empresa) · Notas (CabNota, CorNota) ·
Pedidos (CabPedido, CorPedido) · Entradas (CabEntrada, CorEntrada) ·
Fiscal (ClFiscal, NaturezaOperaco, CondPagto, CstIcm, CstIpi, CstPisCofin) ·
Vendas (Vendedore, GruposProduto, AliquotasIcm, TiposPagto, RamosDeAtividade, CentroCusto) ·
Financeiro (Debito, Credito, ContasCors, FluxoCaixa) · NFe (FocusNfe, Manifesto, Email) ·
Usuarios (Formula)

### Endpoints administrativos
- `api/admin/tenants` (policy `SistemaAdmin`) — tenants, modulos, primeiro admin do tenant
- `api/importador` (policy `SistemaAdmin`) — upload/restauracao de `.bak` e importacao. Contrato:
  restore devolve so `databaseName`; preview/iniciar recebem `databaseName` **ou**
  `connectionStringLegado` (esta so para host em `Importador:HostsPermitidos`, vazio = desligado).
  Ver [[importador-origem-por-databasename-e-allowlist]]
- Dashboard `/hangfire` exige claim `admin_sistema` (mostra jobs de todos os tenants)
- As rotas `api/admin/database/validate-local-backup` e `schema-status` **nao existem mais**
  no codigo (o README ainda as documentava ate 2026-09-11).

### Emissão real de NF-e via Focus (`NotaFiscalEmissaoService`, 2026-09-16)
`CabNotaController` ganhou rotas que fazem a ponte com a Focus NFe de verdade (antes só existia
`FocusNfeController` sob `/api/nfe/{ref}`, que exige o caller montar o `EmitirNfeRequest`
completo — nada no sistema fazia isso, nem para uma nota só):
- `POST /api/notas-fiscais/{id}/emitir` — monta o request a partir de CabNota+CorNota+Cliente+
  Empresa+NaturezaOperaco+TiposPagto (`NotaFiscalEmissaoService.MontarRequisicaoAsync`), chama a
  Focus e grava `RefFocus`/`StatusFocus` de volta.
- `GET .../itens`, `POST .../consultar-status`, `POST .../cancelar`, `POST .../enviar-email`,
  `GET .../xml`, `GET .../danfe`, `GET .../lote/exportar-xml` (zip) — todas reais agora.
- `CabNotaService.AtualizarStatusFocusPorRefAsync` também traduz o vocabulário da Focus
  (autorizado/cancelado/...) pro vocabulário legado que as telas leem em `CabNota.Status`
  (VALIDA/CANCELADA/...) e grava `Idnfe`/`Idprotocolo` — antes só gravava `StatusFocus`/`ChaveNfe`
  (campos novos, paralelos), a tela nunca via a mudança de status de verdade.
- **Bloqueio de dado real, não de integração**: NCM é obrigatório por item na Focus e a base
  legada nunca teve essa coluna em `Produtos` — nem no schema, nem em nenhuma tabela relacionada.
  Migration `017_produto_ncm.sql` + campo `Produto.Ncm` novos; o mapper recusa emitir (400) e lista
  quais produtos estão sem NCM, em vez de inventar um valor.
- Limitações conhecidas, não cobertas: DIFAL (partilha ICMS consumidor final outro estado),
  Imposto de Importação (II), NFC-e (`EmitirNfceRequest`) não auditado pela mesma revisão.
- Ver [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]] (achado mais grave desta
  rodada) e [[2026-09-16-btechplus-frontend-comparacao-telas]] (Fase 6).

### Dashboard e Notificações reais (2026-09-16, tarde)
`GET /api/dashboard/kpis` (`DashboardController`/`DashboardService`) — série mensal real (7 meses,
mais antigo→mais recente) de CabNota (modelo 55, por `DataEmissao`)/Cliente/Produto/Empresa (por
`DataCadastro`), + variação % mes atual vs anterior, + `temCertificadoValido` (algum `Certificado`
ativo com `TerminoValidade` no futuro). `IRepository<T>` ganhou `CountAsync(predicate)` (SQL
`COUNT`, não materializa a entidade) — usado 28x aqui (4 entidades x 7 meses), evitar reaproveitar
`FindAsync().Count()` pra isso em tabelas grandes.

`GET/POST /api/notificacoes` (`NotificacaoController`/`NotificacaoService`) — notificações **não
são armazenadas**, são recomputadas a cada consulta a partir do estado real: certificado a
vencer/vencido (janela = `Certificado.AvisoVencCertificado`, default 30 dias), nota rejeitada pela
SEFAZ (`CabNota.StatusFocus` = erro_autorizacao/denegado) e nota em contingência
(`CabNota.ModoContingencia`). Só o "já vi essa" persiste — tabela nova `NotificacoesLidas`
(`CodUsuario + Chave`, migration `018_notificacoes_lidas.sql`), mesmo padrão de
`ConfiguracoesTabela` (migration 007). "Marcar todas lidas" recomputa os alertas atuais e insere
uma linha por chave ainda não lida.


### Focus NFe por empresa e código sequencial (2026-09-26)
Token Focus por empresa/ambiente em `EmpresasFocus` (migração 021), cifrado com
`CRIPTOGRAFIA_CHAVE` — ver [[focus-token-por-empresa-cifrado]]. Cadastros IDENTITY usam
`AdicionarComCodigoSequencialAsync` ([[identity-e-contador-da-tabela-inteira-em-multi-tenant]]).
Busca `?busca=` em clientes/fornecedores/vendedores/estoque. Sessão:
[[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]].


### Conferência fiscal da NF-e (2026-09-28)
`GET /api/notas-fiscais/{id}/conferencia-fiscal` lista pendências com `corrigirEm`; emitir recusa
antes da Focus. Campos fiscais do legado: [[conclusao-tirada-do-schema-sem-olhar-os-dados]].
Sessão: [[2026-09-28-btech-conferencia-fiscal-da-nota]] (PR #44).

### NFS-e por catálogo de campos (2026-09-29)
`NfseCatalogo` lista todos os campos da Focus (municipal `/v2/nfse` e Nacional `/v2/nfsen`); tela, payload e pendências saem dele. Migração 023 (`EmpresasNfse`, `ServicosNfse`, rastreio em `CabNFe_Srv`), `/api/nfse/...`, `/api/servicos-nfse`. Sessão: [[2026-09-29-btech-nfse-completa-e-catalogo-de-campos]] (API#46, Web#30).

## Relacionado
- [[banco-em-branco-com-importacao-por-painel]]
- [[schema-base-sqlserver-gerado-com-smo]]
- [[arquitetura-dotnet-em-camadas]]
- [[migrations-sql-manuais-em-vez-de-ef-migrations]]
- [[repositorio-generico-e-servicebase]]
- [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]]
- [[BTech.Web]]
- [[dotnet-10]]

## Atualização 2026-10-01 — devolução, importação, número da nota
- `CabNota`: devolução (`NfeReferenciada`+`NroNfeRef` → finalidade 4 + `notas_referenciadas`), importação (`Nfimportacao` + DI → `items[].documentos_importacao`), número gravado em `AtualizarStatusFocusPorRefAsync`, estoque via `MovimentarPorNotaAsync` na autorização. 5 testes `NfseEnvioMutacaoTests` já falham na main. Detalhe em [[2026-10-01-btech-nota-unica-produto-servico-devolucao-importacao]].
