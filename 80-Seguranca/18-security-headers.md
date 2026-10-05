---
tipo: seguranca
titulo: 18. Adicionar security headers
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [header, csp, content security policy, hsts, x-frame-options, nosniff, referrer policy, permissions policy, clickjacking, xss, front, web, site, navegador, next config, middleware, deploy]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 18. Adicionar security headers
## Resumo
Toda resposta HTTP carrega os headers de defesa: CSP, nosniff, anti-clickjacking, referrer e permissions policy.

## Contexto
Regra 18 das 20 obrigatorias.

## Detalhe

### O conjunto minimo
| Header | Valor | Evita |
| --- | --- | --- |
| `Content-Security-Policy` | `default-src 'self'` (ajustar) | XSS, injecao de script de terceiro |
| `X-Content-Type-Options` | `nosniff` | browser adivinhar tipo e executar upload como script |
| `X-Frame-Options` | `DENY` (ou CSP `frame-ancestors`) | clickjacking |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | vazar URL interna com id/token |
| `Permissions-Policy` | `camera=(), microphone=(), geolocation=()` | API sensivel sem querer |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` | downgrade para HTTP → [[19-forcar-https]] |

### CSP e o unico dificil
Comece em `Content-Security-Policy-Report-Only`, colete os relatorios, ajuste, so entao imponha.
Impor CSP de primeira quebra a aplicacao e ensina o time a desligar.

### Onde configurar
- **.NET**: middleware proprio no comeco do pipeline, ou pacote `NetEscapades.AspNetCore.SecurityHeaders`
- **Next**: `async headers()` no `next.config.ts`

### No seu codigo hoje
[[BTech.NFe.Api]] tem headers configurados (com teste de integracao — bom sinal).
[[Heavy]], [[BTech.Web]] e [[ICook]] **nao tem**. Como o BTech.Web e a tela que o usuario abre,
e onde CSP mais importa: e o alvo real de XSS.

## Relacionado
- [[19-forcar-https]]
- [[16-restringir-upload-de-arquivos]]
