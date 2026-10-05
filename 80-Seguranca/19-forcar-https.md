---
tipo: seguranca
titulo: 19. Forcar HTTPS
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [https, tls, hsts, redirect, certificado, ssl, downgrade, man in the middle, cookie secure, deploy, producao, publicar, dominio, nginx, azure]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 19. Forcar HTTPS
## Resumo
HTTP redireciona para HTTPS e o navegador e instruido a nunca mais tentar HTTP (HSTS).

## Contexto
Regra 19 das 20 obrigatorias.

## Detalhe

### As duas metades
```csharp
app.UseHsts();                 // so em producao
app.UseHttpsRedirection();
```
- **Redirect** resolve a requisicao de agora
- **HSTS** resolve as proximas: o browser passa a recusar HTTP sozinho, sem ida ao servidor

Ter so o redirect deixa a **primeira** requisicao viajar em claro — e ali que o ataque acontece.

### Cuidados com HSTS
- `max-age` alto (1 ano) e **grudento**: o browser nao esquece. Teste com `max-age` baixo antes
- `includeSubDomains` afeta todo subdominio — inclusive o que ainda esta em HTTP
- Nao ligue HSTS em `localhost`; por isso `UseHsts()` fica fora do ambiente de desenvolvimento

### Quando o TLS termina antes da app
Vercel, Azure Front Door e proxies terminam TLS na borda. Ai:
1. Configure HSTS **na borda**
2. Na app, `UseForwardedHeaders` para ela saber que a requisicao original era HTTPS —
   senao `UseHttpsRedirection` entra em loop de redirecionamento

### No seu codigo hoje
[[BTech.NFe.Api]]: `UseHttpsRedirection` + `UseHsts`. Certo.
[[Heavy]]: **nenhum dos dois**. [[BTech.Web]]: HTTPS vem da borda — confirmar HSTS la.
[[Sites-Estaticos]]: garantir no host.

## Relacionado
- [[18-security-headers]]
- [[09-proteger-cookies-de-sessao]]
