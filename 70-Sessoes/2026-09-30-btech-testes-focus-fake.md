---
tipo: sessao
titulo: "BTech: frente FOCUS FALSA — integração ponta a ponta da API com um servidor Focus de mentira e 4 bugs achados"
projeto: [BTech.NFe.Api]
stack: [dotnet, xunit, webapplicationfactory, focus-nfe, inmemory]
tags: [tipo/sessao, focus-nfe, testes, integracao, concorrencia]
palavras-chave: [focus falsa, FocusFalsa, WebApplicationFactory, HttpClient FocusNfe, ConfigurePrimaryHttpMessageHandler, Polly, reenvio idempotente, ref reservada, erro_cancelamento, webhook, token por empresa, tenant, faturar pedido misto]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# BTech: frente FOCUS FALSA

## Resumo
API PR #83 (`test/focus-fake-integracao`). Projeto `tests/BTech.NFe.Tests.FocusFake`: API em memória + `FocusFalsa` (handler que imita a Focus v2 e valida o que ela valida) no lugar do HttpClient `FocusNfe`. 117 testes, ~20 s, sem rede/Docker. Cobre NF-e/NFC-e/NFS-e (municipal e nacional), erros 422/401/403/5xx/timeout, cancelamento, CC-e, inutilização, webhooks com/sem segredo, token por empresa/ambiente/tenant, concorrência e FATURAR pedido misto até a autorização. Ver [[BTech.NFe.Api]] e [[focus-token-por-empresa-cifrado]].

## Detalhe
- **Como trocar a Focus**: `services.Configure<HttpClientFactoryOptions>("FocusNfe", o => { o.HttpMessageHandlerBuilderActions.Clear(); o.HttpMessageHandlerBuilderActions.Add(b => b.PrimaryHandler = fake); })`. O `Clear()` tira o Polly (retry 2/4/8 s por 5xx: custaria 14 s por teste); por isso retry e circuit breaker NÃO são cobertos. Antes, `ConfigureAll` põe um `SemRede` em todo cliente, senão ViaCEP (pré-preenchimento da NFS-e) sai para a internet. `Dispose` do handler tem de ser no-op: a fábrica o descarta após 2 min.
- **Um host só**: subir a API custa ~15 s (JIT). `[Collection]` + `ICollectionFixture` para a maioria; hosts extras (sem segredo de webhook, token global) saem baratos depois do primeiro. Ler `Tenant1` antes do host existir devolve 0: o getter força `_ = Services`.
- **Concorrência sem sleep**: regra assíncrona na fake que sinaliza `Chegou` e espera `Liberar`; o teste dispara o 1º pedido, espera `Chegou`, dispara o 2º, libera. Só funciona porque a nota é reservada (ref + processando) ANTES do POST.
- **Bugs reais** (commits separados no PR): (1) NF-e sem a guarda de "já transmitida" que a NFC-e tinha e ref gravada só depois da Focus; (2) 5xx com HTML vazava o corpo cru da Focus no título do ProblemDetails; (3) NFS-e: 2º POST simultâneo levava 422 "ref já existe" e a nota virava erro_validacao; (4) NFS-e: `erro_cancelamento` (HTTP 200) era gravado como status → nota PROCESSANDO e sem mensagem.
- **Seed do teste**: SitTrib do legado é 3/4 dígitos: Simples = `0102` (origem+CSOSN), regime normal = `000` (origem+CST 00; `0000` é recusado). Empresa tem chave só CodEmpresa no modelo EF, então duas empresas de tenants diferentes precisam de códigos distintos. Política `ConfigurarIntegracaoFiscal` exige claim `admin_sistema` (usuário `suporte`).
- **Risco registrado, não alterado**: com `FocusNfe:Token` global preenchido, empresa sem `EmpresasFocus` usa o global (modo instalação antiga) — em SaaS significa emitir com a credencial de outro tenant; só a Focus (CNPJ x token) barra.
- **Ambiente**: `Edit`/sed `-i` no macOS exige `''`; build .NET trata estilo como erro (IDE0161 namespace com escopo de arquivo, IDE0005). Disco: ~5 GB livres com 5 agentes — bin/obj do worktree apagados no fim.
