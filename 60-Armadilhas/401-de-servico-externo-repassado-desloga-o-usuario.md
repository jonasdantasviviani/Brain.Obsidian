---
tipo: armadilha
titulo: "401 do serviço externo repassado ao navegador desloga o usuário"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, aspnetcore, next]
tags: [tipo/armadilha, integracao, focus-nfe, ux]
palavras-chave: [401, 403, focus nfe, token invalido, sessao expirada, logout, GlobalExceptionHandler, HttpRequestException]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# 401 do serviço externo repassado ao navegador desloga o usuário

## Resumo
`GlobalExceptionHandler` repassava o status HTTP da Focus (`HttpRequestException.StatusCode`) como
status da API. Token da Focus ausente/errado → Focus 401 → API 401 → o `request()` do
[[BTech.Web]] entende "sessão expirada", apaga o token e manda para /login. Parecia que "nada
funciona": cada tentativa de emitir derrubava a sessão.

## Correção
- `FocusNfeService.ParseResponse`: 401/403 vira `InvalidOperationException` com mensagem da Focus
  ("Access token inválido") + onde corrigir → 400.
- Handler global: `HttpRequestException` 401/403 → 502, nunca 401.
- Sem token: recusa antes de chamar a Focus.

## Lição
401/403 de dependência externa é credencial DELA, não do usuário. Nunca repassar.
Ver [[focus-token-por-empresa-cifrado]].

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[focus-token-por-empresa-cifrado]]
- [[failed-to-fetch-no-front-btech]]
- [[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]]
