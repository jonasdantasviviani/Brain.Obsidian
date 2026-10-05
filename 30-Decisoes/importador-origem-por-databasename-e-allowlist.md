---
tipo: decisao
titulo: Importador recebe o nome do banco temporario; connection string ao vivo so com allowlist
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, sqlserver, hangfire, next]
tags: [tipo/decisao, stack/dotnet, empresa/btech, seguranca]
palavras-chave: [importador, importacao, bak, restaurar, stg_import, databaseName, connectionStringLegado, OrigemImportacao, allowlist, HostsPermitidos, ssrf, vazamento, senha sa, hangfire, argumento de job, dashboard, admin_sistema]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Importador recebe o nome do banco temporario; connection string ao vivo so com allowlist
## Resumo
O painel de importacao nao troca mais connection string com o navegador: restore devolve `databaseName`, e preview/iniciar recebem esse nome (ou uma connection string de host liberado em config).

## Contexto
Corrigindo regras [[15-nao-vazar-dados]] e [[23-prevenir-ssrf]] na [[BTech.NFe.Api]]. Antes:
1. `POST /api/importador/backups/{arq}/restaurar` devolvia `connectionString` com `sa` + senha;
2. `preview`/`iniciar` aceitavam qualquer connection string e a API conectava nela;
3. o `iniciar` passava a connection string como argumento do job Hangfire → texto puro em
   `HangFire.Job` e no dashboard, que so exigia `super_usuario` (admin de um tenant).

## Detalhe

### Contrato novo
- `BackupRestauradoDto { DatabaseName }` — so isso.
- `OrigemImportacao { DatabaseName?, ConnectionStringLegado? }` — exatamente um.
  `DatabaseName` precisa casar `^stg_import_[0-9a-f]{12}$` (`BancoTemporarioImportacao`, Domain);
  a conexao sai de `IBackupRestoreRepository.ConnectionStringDoBanco` (Infrastructure.Sql).
- Live: host extraido por `IImportadorLegadoRepository.ObterServidor` e conferido contra
  `Importador:HostsPermitidos` (vazio por padrao = desligado). Recusa `Failover Partner`,
  `AttachDbFilename`, `np:`/`lpc:`/`admin:`.
- Job Hangfire recebe `OrigemImportacao` e **revalida**. Persistido: `{"DatabaseName":"stg_import_..."}`.
- `HangfireDashboardAuthFilter` passou a exigir `admin_sistema`.
- Invalido → `InvalidOperationException` → 400 `{ error }`, sem abrir conexao.

### Alternativas descartadas
| Opcao | Por que nao |
| --- | --- |
| Remover o modo ao vivo | Funcionalidade existente no front; decisao de produto. Ficou atras de allowlist, desligado por padrao |
| Proteger a connection string ao vivo com Data Protection antes do Hangfire | As chaves de Data Protection nao sao persistidas no container (reinicio quebraria job enfileirado) e o modo ja nasce desligado; ficou como risco residual documentado |
| Bloquear faixas de IP privadas em vez de allowlist | Blocklist nao cobre encodings/DNS; cliente legitimo pode estar em VPN/rede privada |

### Risco residual
Com o modo ao vivo **ligado**, a connection string do cliente (credencial dele, nao da API) vai
nos argumentos do job e fica em `HangFire.Job`, visivel so para AdminSistema.

### Verificado
Unit 392→434, Contract 182→199, Functional 28/28; teste de mutacao (tirar a allowlist) derrubou
5 testes de contrato; E2E real em stack isolada: restore so com `databaseName`, 6 ataques → 400,
794 registros importados, `HangFire.Job` sem `Password`.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[15-nao-vazar-dados]]
- [[23-prevenir-ssrf]]
- [[banco-em-branco-com-importacao-por-painel]]
