---
tipo: sessao
titulo: BTech.NFe.Api — importador sem vazar credencial nem abrir SSRF
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, sqlserver, hangfire, next]
tags: [tipo/sessao, stack/dotnet, empresa/btech, seguranca]
palavras-chave: [importador, databaseName, allowlist, HostsPermitidos, ssrf, senha sa, hangfire dashboard, admin_sistema, contrato, testes de contrato, mutacao]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# BTech.NFe.Api — importador sem vazar credencial nem abrir SSRF
## Resumo
Restore devolve so `databaseName`; preview/iniciar recebem `OrigemImportacao`; modo ao vivo atras de `Importador:HostsPermitidos`; dashboard Hangfire so para AdminSistema; front ajustado.

## Contexto
Tarefa aberta a partir do achado de [[2026-09-11-btech-nfe-api-banco-em-branco]]. Decisao em
[[importador-origem-por-databasename-e-allowlist]].

## Detalhe
Backend (nao commitado; o working tree ja tinha o trabalho do webhook Focus e a mudanca do banco
em branco):
- novos: `Domain/Models/BancoTemporarioImportacao.cs`, `Domain/Models/OrigemImportacao.cs`
- alterados: DTOs, `IImportadorService`, `IBackupRestoreRepository` (`ConnectionStringDoBanco`),
  `IImportadorLegadoRepository` (`ObterServidor`), `ImportadorService`, `BackupRestoreService`,
  repositorios, `ImportadorController`, `HangfireDashboardAuthFilter`, `appsettings.json`
  (`Importador:HostsPermitidos: []`), `docs/API.md` (secao nova), README, INSTALLATION, CLAUDE.md
- testes: `ImportadorContractTests` (17), `ImportadorServiceTests`, `BancoTemporarioImportacaoTests`,
  `ImportadorLegadoRepositoryObterServidorTests`, `HangfireDashboardAuthFilterTests`; teste de
  integracao do importador adaptado (modo ao vivo com `localhost` na allowlist — so roda com SQL
  Server local de Windows, nao executado aqui)

Front ([[BTech.Web]]): `src/lib/api.ts` (tipo `OrigemImportacao`, `restaurar` so com
`databaseName`) e `src/app/admin/importacao/page.tsx` (aba padrao `.bak`, preview/iniciar pela
origem ativa, botao "Pre-visualizar" no aviso do banco temporario). **Type-check nao rodou**:
`node_modules` evictado pelo iCloud ([[icloud-evicta-node-modules-e-tsc-trava]]) — revisado a mao.

Resultados: Unit 434, Contract 199, Functional 28, todos verdes; mutacao derrubou 5 testes; E2E real
isolado ok. O `btech-api` local do Jonas ainda roda a imagem anterior ate `docker compose up -d --build`.

**Commitado e publicado** (a pedido do Jonas) na branch `feature/banco-em-branco-e-importador-seguro`,
criada de `origin/main` porque `security/checklist-20-regras` ja tinha sido mergeada por squash
([[branch-mergeada-por-squash-e-apagada-no-remoto]]):
- API: `7282ebe` checklist de seguranca (trabalho que ja estava pendente), `3723aee` banco em branco,
  `eaf629b` importador — separados com [[separar-working-tree-em-commits-sem-add-p]]
- Web: `c4fa30a` tela de importacao
PRs abertos: API #13 e Web #8 (via `GH_TOKEN` com a credencial do osxkeychain — o token so tem
`repo`/`workflow`, entao `gh pr edit`/`gh pr list` (GraphQL, pedem `read:org`) falham; usar `gh api` REST).
O Jonas mergeou o #13 (e o #11) em seguida, **com o CI vermelho** — Web #8 ficou aberto.

A main estava quebrada ([[dependabot-mergeado-com-ci-vermelho-quebra-a-main]]): aberto o **#14**
(`fix/ci-main`) — Hangfire alinhado em 1.8.25, `ConfigureSwaggerOptions` migrado para Swashbuckle 10,
teste de integracao com SQL Server fora do CI por trait. Verde no GitHub (Build & Test + Docker Build)
depois de `update-branch` com a main atual. #9 e #10 foram fechados pelo Dependabot enquanto eu
trabalhava (meu `@dependabot rebase` no #10 caiu num PR ja fechado). #9 esbarra em licenca:
[[fluentassertions-8-exige-licenca-comercial]].

Depois: o Jonas mergeou #14 e Web #8 (main verde de novo) e pediu a regra do Dependabot → PR #15
(`chore/dependabot-fluentassertions-hangfire`): `ignore` FluentAssertions `>= 8.0.0` e grupo
`hangfire` antes do generico; validado contra o schema oficial; CI verde.

Descoberta de teste: `JsonSerializer.Serialize(job.Args)` quebra no `CancellationToken`; para ver
o que o Hangfire persiste use `InvocationData.SerializeJob(job).Arguments`. `AspNetCoreDashboardContext`
exige `HttpContext.RequestServices` preenchido.

## Relacionado
- [[importador-origem-por-databasename-e-allowlist]]
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[icloud-evicta-node-modules-e-tsc-trava]]
