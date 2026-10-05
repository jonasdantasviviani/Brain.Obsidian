---
tipo: seguranca
titulo: 22. CORS restritivo
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [cors, origem, origin, allowanyorigin, credentials, preflight, wildcard, frontend, navegador, api]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 22. CORS restritivo
## Resumo
Origens listadas explicitamente, nunca `*` junto de credenciais, e nunca refletindo a origem que o cliente mandou.

## Contexto
Regra 22. A [[BTech.NFe.Api]] **ja faz certo** — esta nota existe para travar o padrao, porque
CORS e a configuracao que mais apanha "correcao" errada quando alguem esbarra num erro no
navegador.

## Detalhe

### O que esta certo hoje
```csharp
var corsOrigins = builder.Configuration.GetSection("Cors:Origins").Get<string[]>() ?? [];
policy.WithOrigins(corsOrigins).AllowAnyHeader().AllowAnyMethod().AllowCredentials();
```
Origens vem da configuracao, por ambiente. Producao lista so o dominio do front.

### O erro que vai tentar acontecer
Alguem ve `blocked by CORS policy` no console e "resolve" assim:
```csharp
policy.AllowAnyOrigin().AllowAnyHeader().AllowAnyMethod();          // some com credencial
policy.SetIsOriginAllowed(_ => true).AllowCredentials();            // PIOR: reflete qualquer origem
```
A segunda linha e a mais perigosa: com `AllowCredentials`, **qualquer site** passa a poder fazer
requisicoes autenticadas em nome do usuario logado. E CSRF com aprovacao do servidor.

O proprio ASP.NET Core **lanca excecao** ao combinar `AllowAnyOrigin()` com `AllowCredentials()`.
Quando alguem contorna isso com `SetIsOriginAllowed(_ => true)`, esta contornando uma protecao
de proposito — trate como bug de seguranca, nao como ajuste.

### Lembretes
- CORS protege o **navegador**, nao a API. `curl` ignora CORS. Ele nunca substitui
  [[06-auth-no-servidor]].
- Subdominio e origem diferente: `app.exemplo.com` nao cobre `admin.exemplo.com`.
- Protocolo e porta contam: `http://` e `https://` sao origens distintas.

## Relacionado
- [[06-auth-no-servidor]]
- [[09-proteger-cookies-de-sessao]]
