---
tipo: seguranca
titulo: Checklist de Seguranca
projeto: [todos]
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [seguranca, checklist, auditoria, 20 regras, vulnerabilidade, pendencia, corrigir]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-10-05
confianca: alta
---

# Checklist de Seguranca

> Gerado automaticamente em 2026-10-05 16:13 pela auditoria do Cerebro.
> Rastreador por evidencia: **ok = achei sinal**, nao = prova de que esta correto.

## As 20 regras

01. [[01-esconder-api-keys|Esconder API keys]]
02. [[02-limpar-secrets-do-git|Limpar secrets do git]]
03. [[03-chave-publica-do-banco|Só chave pública do banco no cliente]]
04. [[04-ativar-rls|Ativar RLS]]
05. [[05-criptografar-dados-sensiveis|Criptografia de dados sensíveis]]
06. [[06-auth-no-servidor|Auth server side]]
07. [[07-travar-acesso-aos-registros|Travar acesso aos registros]]
08. [[08-bloquear-mass-assignment|Bloquear mass assignment]]
09. [[09-proteger-cookies-de-sessao|Proteger cookies da sessão]]
10. [[10-hash-nas-senhas|Hash nas senhas]]
11. [[11-rate-limit-no-login|Rate limit no login]]
12. [[12-bot-protection|Bot protection]]
13. [[13-queries-parametrizadas|Queries parametrizadas]]
14. [[14-validacao-dos-inputs|Validação dos inputs]]
15. [[15-nao-vazar-dados|Não vazar dados]]
16. [[16-restringir-upload-de-arquivos|Restringir upload de arquivos]]
17. [[17-trim-nas-respostas-de-api|Trim nas respostas de API]]
18. [[18-security-headers|Adicionar security headers]]
19. [[19-forcar-https|Forçar HTTPS]]
20. [[20-scan-de-dependencias|Scan de dependências]]
21. [[21-nao-expor-docs-em-producao|Não expor docs em produção]]
22. [[22-cors-restritivo|CORS restritivo]]
23. [[23-prevenir-ssrf|Prevenir SSRF]]
24. [[24-webhooks-seguros|Webhooks seguros]]
25. [[25-ciclo-de-vida-da-sessao|Ciclo de vida da sessão]]
26. [[26-trilha-de-auditoria|Trilha de auditoria]]
27. [[27-seguranca-do-app-mobile|Segurança do app mobile]]
28. [[28-backup-testado-e-protegido|Backup testado e protegido]]

## Estado por projeto

| Projeto | Stack | ok | corrigir | revisar |
| --- | --- | --- | --- | --- |
| BTech.NFe.Api | dotnet, sql, sqlserver | 15 | **5** | 5 |
| BTech.Web | next, node | 9 | **1** | 0 |
| BTech | dotnet, node, sql, sqlserver | 16 | **4** | 5 |
| Eden | dotnet, flutter, node, postg | 14 | **9** | 3 |
| Heavy | dotnet, flutter, postgres, s | 17 | **6** | 2 |
| ICook | dotnet, flutter, postgres | 10 | **7** | 1 |
| btech-nfe-web | next, node | 9 | **1** | 1 |
| counter-ragdoll | flutter | 2 | **3** | 1 |
| hub | estatico | 2 | **0** | 2 |
| mac-local-setup-2a3a5c |  | 0 | **0** | 1 |
| need-for-ragdoll | flutter | 5 | **2** | 1 |
| restaurante | estatico | 1 | **0** | 2 |

## Pendencias abertas, agrupadas por regra

### 01. Esconder API keys — 7 projeto(s)
Regra: [[01-esconder-api-keys]]

- **Eden** · revisar — possivel segredo literal: chave OpenAI/Anthropic em tests/Eden.Api.Tests/AgentT420Tests.cs; AWS access key em tests/Eden.Infrastructure.Tests/RedactorTests.cs; token GitHub em tests/Eden.Infrastructure.Tests/RedactorTests.cs
- **ICook** · revisar — nao encontrei nem segredo literal nem leitura de ambiente
- **counter-ragdoll** · revisar — nao encontrei nem segredo literal nem leitura de ambiente
- **hub** · revisar — nao encontrei nem segredo literal nem leitura de ambiente
- **mac-local-setup-2a3a5c** · revisar — nao encontrei nem segredo literal nem leitura de ambiente
- **need-for-ragdoll** · revisar — nao encontrei nem segredo literal nem leitura de ambiente
- **restaurante** · revisar — nao encontrei nem segredo literal nem leitura de ambiente

### 02. Limpar secrets do git — 2 projeto(s)
Regra: [[02-limpar-secrets-do-git]]

- **BTech.NFe.Api** · revisar — valor real em arquivo versionado: src/WebApi/BTech.NFe.Api/appsettings.Development.json → Key
- **Heavy** · revisar — valor real em arquivo versionado: backend/src/WebApi/HeavyOps.WebApi/appsettings.Development.json → ConnectionString; backend/src/WebApi/HeavyOps.WebApi/appsettings.Development.json → ConnectionString

### 04. Ativar RLS — 4 projeto(s)
Regra: [[04-ativar-rls]]

- **BTech.NFe.Api** · revisar — SQL Server sem RLS nativo; ha HasQueryFilter (checar se cobre tudo)
- **BTech** · revisar — SQL Server sem RLS nativo; ha HasQueryFilter (checar se cobre tudo)
- **Eden** · CORRIGIR — Postgres sem ROW LEVEL SECURITY no schema
- **ICook** · CORRIGIR — Postgres sem ROW LEVEL SECURITY no schema

### 05. Criptografia de dados sensíveis — 3 projeto(s)
Regra: [[05-criptografar-dados-sensiveis]]

- **BTech.NFe.Api** · CORRIGIR — cnpj, cpf, rg em claro (000_schema_base.sql, 002_create_eve_manifestacao.sql) - avaliar cripto ou mascaramento
- **BTech** · CORRIGIR — cnpj, cpf, rg em claro (000_schema_base.sql, 002_create_eve_manifestacao.sql) - avaliar cripto ou mascaramento
- **Heavy** · CORRIGIR — cartao, cnpj, cpf em claro (20260813005100_Inicial.Designer.cs, 20260813005100_Inicial.cs) - avaliar cripto ou mascaramento

### 06. Auth server side — 2 projeto(s)
Regra: [[06-auth-no-servidor]]

- **ICook** · CORRIGIR — sem AddAuthentication nem [Authorize] no backend
- **btech-nfe-web** · revisar — front puro guardando token em localStorage - preferir cookie HttpOnly

### 07. Travar acesso aos registros — 2 projeto(s)
Regra: [[07-travar-acesso-aos-registros]]

- **Eden** · CORRIGIR — nao achei filtro por dono/tenant - risco de IDOR
- **ICook** · CORRIGIR — nao achei filtro por dono/tenant - risco de IDOR

### 08. Bloquear mass assignment — 2 projeto(s)
Regra: [[08-bloquear-mass-assignment]]

- **BTech.NFe.Api** · revisar — [FromBody] com tipo que parece entidade: AliquotasIcmController.cs:AliquotasIcm, AliquotasIcmController.cs:JsonElement, CabEntradaController.cs:CabEntrada, CabEntradaController.cs:JsonElement
- **BTech** · revisar — [FromBody] com tipo que parece entidade: AliquotasIcmController.cs:AliquotasIcm, CabEntradaController.cs:CabEntrada, CabNotaController.cs:CabNota, CabPedidoController.cs:CabPedido

### 10. Hash nas senhas — 3 projeto(s)
Regra: [[10-hash-nas-senhas]]

- **BTech** · revisar — ha hash forte, mas MD5/SHA1 aparece em: BTech.Web/package-lock.json
- **Eden** · revisar — ha hash forte, mas MD5/SHA1 aparece em: scripts/seed-demo.sql
- **Heavy** · CORRIGIR — nao achei algoritmo de hash de senha

### 12. Bot protection — 6 projeto(s)
Regra: [[12-bot-protection]]

- **BTech.NFe.Api** · CORRIGIR — formulario publico sem captcha/turnstile/honeypot
- **BTech.Web** · CORRIGIR — formulario publico sem captcha/turnstile/honeypot
- **BTech** · CORRIGIR — formulario publico sem captcha/turnstile/honeypot
- **Eden** · CORRIGIR — formulario publico sem captcha/turnstile/honeypot
- **Heavy** · CORRIGIR — formulario publico sem captcha/turnstile/honeypot
- **btech-nfe-web** · CORRIGIR — formulario publico sem captcha/turnstile/honeypot

### 15. Não vazar dados — 3 projeto(s)
Regra: [[15-nao-vazar-dados]]

- **BTech.NFe.Api** · revisar — excecao devolvida na resposta
- **BTech** · revisar — excecao devolvida na resposta
- **Eden** · revisar — excecao devolvida na resposta

### 17. Trim nas respostas de API — 3 projeto(s)
Regra: [[17-trim-nas-respostas-de-api]]

- **BTech.NFe.Api** · revisar — controller devolvendo tipo sem sufixo Dto: AliquotasIcm→AliquotasIcm, CabEntrada→CabEntrada, CabNota→CabNota, CabPedido→CabPedido
- **BTech** · revisar — controller devolvendo tipo sem sufixo Dto: AliquotasIcm→AliquotasIcm, CabEntrada→CabEntrada, CabNota→CabNota, CabPedido→CabPedido
- **ICook** · CORRIGIR — sem DTO de saida - risco de devolver entidade inteira

### 18. Adicionar security headers — 1 projeto(s)
Regra: [[18-security-headers]]

- **Eden** · CORRIGIR — sem CSP / X-Content-Type-Options / X-Frame-Options / Referrer-Policy

### 19. Forçar HTTPS — 3 projeto(s)
Regra: [[19-forcar-https]]

- **Eden** · CORRIGIR — sem UseHttpsRedirection nem UseHsts
- **hub** · revisar — site estatico: garantir HTTPS + HSTS no host
- **restaurante** · revisar — site estatico: garantir HTTPS + HSTS no host

### 20. Scan de dependências — 3 projeto(s)
Regra: [[20-scan-de-dependencias]]

- **BTech** · CORRIGIR — sem dependabot nem scan de vulnerabilidade no CI
- **Eden** · CORRIGIR — sem dependabot nem scan de vulnerabilidade no CI
- **counter-ragdoll** · CORRIGIR — sem dependabot nem scan de vulnerabilidade no CI

### 21. Não expor docs em produção — 1 projeto(s)
Regra: [[21-nao-expor-docs-em-producao]]

- **Eden** · CORRIGIR — MapOpenApi sem guarda de ambiente em src/Eden.Api/Program.cs

### 24. Webhooks seguros — 1 projeto(s)
Regra: [[24-webhooks-seguros]]

- **Heavy** · revisar — webhook mencionado mas nao localizei o handler

### 25. Ciclo de vida da sessão — 3 projeto(s)
Regra: [[25-ciclo-de-vida-da-sessao]]

- **BTech.NFe.Api** · CORRIGIR — JWT valida expiracao mas nao ha revogacao - logout nao invalida o token
- **Eden** · CORRIGIR — JWT sem ValidateLifetime = true
- **ICook** · CORRIGIR — JWT sem ValidateLifetime = true

### 26. Trilha de auditoria — 3 projeto(s)
Regra: [[26-trilha-de-auditoria]]

- **BTech.NFe.Api** · CORRIGIR — sem trilha de auditoria (log de aplicacao nao substitui)
- **Heavy** · CORRIGIR — sem trilha de auditoria (log de aplicacao nao substitui)
- **ICook** · CORRIGIR — sem trilha de auditoria (log de aplicacao nao substitui)

### 27. Segurança do app mobile — 3 projeto(s)
Regra: [[27-seguranca-do-app-mobile]]

- **Heavy** · CORRIGIR — sem flutter_secure_storage
- **counter-ragdoll** · CORRIGIR — sem flutter_secure_storage
- **need-for-ragdoll** · CORRIGIR — sem flutter_secure_storage

### 28. Backup testado e protegido — 7 projeto(s)
Regra: [[28-backup-testado-e-protegido]]

- **BTech.NFe.Api** · CORRIGIR — backup mencionado sem teste de restauracao visivel
- **BTech** · CORRIGIR — backup mencionado sem teste de restauracao visivel
- **Eden** · CORRIGIR — backup mencionado sem teste de restauracao visivel
- **Heavy** · CORRIGIR — backup mencionado sem teste de restauracao visivel
- **ICook** · CORRIGIR — backup mencionado sem teste de restauracao visivel
- **counter-ragdoll** · CORRIGIR — backup mencionado sem teste de restauracao visivel
- **need-for-ragdoll** · CORRIGIR — backup mencionado sem teste de restauracao visivel

## Relacionado

- [[Protocolo-Cerebro]]
- [[Cerebro]]
