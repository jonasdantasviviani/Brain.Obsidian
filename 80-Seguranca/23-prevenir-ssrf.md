---
tipo: seguranca
titulo: 23. Prevenir SSRF (URL controlada pelo usuario)
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [ssrf, url, requisicao, httpclient, webhook, callback, metadata, rede interna, integracao, importar de url, imagem, pdf, connection string, sql server, importador, allowlist de host, failover partner]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-11
confianca: alta
---

# 23. Prevenir SSRF (URL controlada pelo usuario)
## Resumo
O servidor nunca busca uma URL que o usuario escolheu, sem allowlist. Ele esta dentro da rede — o atacante nao.

## Contexto
Regra 23. Relevante porque [[BTech.NFe.Api]] e [[Heavy]] fazem chamadas externas
(Focus NFe, mapas) e porque a funcionalidade "importar de uma URL" e pedida cedo ou tarde em
todo sistema.

## Detalhe

### O ataque
O usuario manda `http://169.254.169.254/latest/meta-data/iam/security-credentials/` e o
**servidor** busca. Como ele esta dentro da rede, alcanca o que o atacante nao alcanca:
- endpoint de metadados da nuvem (credenciais da instancia)
- `localhost` e servicos internos sem autenticacao (Redis, Elasticsearch, painel de admin)
- o proprio banco

O que volta pode nem ser exibido: **SSRF cego** ainda serve para varrer a rede interna medindo
tempo de resposta.

### Como esta hoje (correto)
No `FocusNfeService`, a URL base vem da **configuracao**, nao do usuario:
```csharp
_http.BaseAddress = new Uri(_options.BaseUrl + "/");
await _http.PostAsync($"nfe?ref={Uri.EscapeDataString(refNfe)}", content, ct);
```
Só o `ref` vem de fora, e vai escapado, como parametro — nao como host. **Esse e o padrao a
manter.**

### Quando aparecer "buscar de uma URL"
1. **Allowlist de host**, nunca blocklist. Bloquear `169.254.169.254` nao cobre
   `0x0.0x0.0x0.0x0`, `[::ffff:169.254.169.254]`, encurtador de URL nem DNS que resolve para IP
   interno.
2. **Resolva o DNS antes e valide o IP** — recusar faixas privadas (10/8, 172.16/12, 192.168/16,
   127/8, 169.254/16, ::1, fc00::/7). Reresolva na hora de conectar ou use um handler que valide
   o IP final, por causa de **DNS rebinding**.
3. **Nao siga redirecionamento** (`AllowAutoRedirect = false`) — o alvo pode redirecionar para
   um IP interno depois de passar na validacao.
4. **Timeout curto e resposta limitada em tamanho.**
5. Idealmente, saida por **proxy dedicado** com allowlist, sem rota para a rede interna.

### SSRF por connection string (achado e corrigido em 2026-09-11)
Nao e so URL: **connection string de banco vinda do cliente e SSRF** — o servidor abre TCP para
onde ela mandar, com a rede interna ao alcance (`Server=sqlserver,1433`, `169.254.169.254`).
Na [[BTech.NFe.Api]], `POST /api/importador/preview` e `/iniciar` aceitavam `connectionStringLegado`
livre. Correcao ([[importador-origem-por-databasename-e-allowlist]]):
- origem padrao e o **nome** do banco temporario (`stg_import_` + 12 hex), com a conexao montada no
  servidor;
- connection string ao vivo so para host em `Importador:HostsPermitidos` (vazio = desligado),
  comparando o host extraido com `SqlConnectionStringBuilder` — e recusando `Failover Partner`
  (segundo host escondido), `AttachDbFilename` e `np:`/`lpc:`/`admin:` (recursos locais);
- validar **antes** de abrir conexao e de novo dentro do job (argumento persistido nao e confiavel).

### Onde mais isso mora
Renderizacao de PDF/HTML no servidor, preview de link, importacao de imagem por URL, webhook de
saida configuravel pelo cliente ([[24-webhooks-seguros]]).

## Relacionado
- [[24-webhooks-seguros]]
- [[14-validacao-dos-inputs]]
- [[BTech.NFe.Api]]
