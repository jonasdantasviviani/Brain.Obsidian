---
tipo: seguranca
titulo: 09. Proteger cookies da sessao
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [cookie, sessao, httponly, secure, samesite, csrf, xss, token, localstorage, login, manter conectado, front]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 09. Proteger cookies da sessao
## Resumo
Cookie de sessao com `HttpOnly`, `Secure` e `SameSite`. Token de sessao nunca em `localStorage`.

## Contexto
Regra 9 das 20 obrigatorias.

## Detalhe

### Os tres atributos, e o que cada um evita
| Atributo | Evita |
| --- | --- |
| `HttpOnly` | JavaScript ler o cookie — mata roubo de sessao via XSS |
| `Secure` | cookie viajar em HTTP puro |
| `SameSite=Lax` ou `Strict` | o cookie ir junto em requisicao de outro site (CSRF) |

```csharp
Response.Cookies.Append("sessao", token, new CookieOptions {
    HttpOnly = true, Secure = true, SameSite = SameSiteMode.Lax,
    Expires = DateTimeOffset.UtcNow.AddHours(8)
});
```

### `localStorage` nao serve para token de sessao
Qualquer script na pagina le `localStorage` — inclusive um script de terceiro comprometido.
Cookie `HttpOnly` e o unico armazenamento que o JS nao alcanca.

### Quando esta regra e N/A
API puramente stateless com Bearer token de vida curta consumida por app nativo. Ai o cuidado
muda de lugar: armazenamento seguro do device (Keychain/Keystore) e refresh token rotativo.

### No seu codigo hoje
Nem [[BTech.NFe.Api]] nem [[Heavy]] usam cookie de sessao — ambos sao Bearer/JWT.
A regra vale para o dia em que o [[BTech.Web]] guardar sessao (ja usa `js-cookie`: **conferir os
atributos**).

## Relacionado
- [[06-auth-no-servidor]]
- [[19-forcar-https]]
