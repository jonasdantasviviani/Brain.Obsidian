---
tipo: seguranca
titulo: 25. Ciclo de vida da sessao (expirar, revogar, renovar)
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [sessao, jwt, token, expiracao, revogacao, logout, refresh, blacklist, demissao, troca de senha, roubo de token]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 25. Ciclo de vida da sessao (expirar, revogar, renovar)
## Resumo
Token expira rapido, logout invalida de verdade e existe um jeito de revogar antes da hora.

## Contexto
Regra 25. Na [[BTech.NFe.Api]] o JWT vale **8 horas** (`Jwt:ExpiresInHours`, padrao 8) com
`ValidateLifetime = true` — e **nao ha revogacao**.

## Detalhe

### O problema do JWT puro
JWT e autocontido: o servidor valida a assinatura sem consultar nada. Isso e a vantagem — e a
armadilha. Nesse desenho, **nao existe logout de verdade**:

| Situacao | O que acontece hoje |
| --- | --- |
| usuario clica em sair | o front descarta o token; o token continua valido por ate 8h |
| token e roubado | vale por ate 8h, e nao ha como cortar |
| usuario e demitido / perde permissao | continua entrando por ate 8h |
| usuario troca a senha por suspeita de invasao | as sessoes antigas seguem valendo |

Oito horas e muito tempo para um token que nao pode ser cancelado.

### Os caminhos, do mais simples ao mais completo
1. **Encurtar o access token** (15-30 min) + **refresh token** de vida longa, guardado no
   servidor e revogavel. O access continua stateless; o refresh vira o ponto de controle.
2. **Rotacionar o refresh a cada uso** e detectar reuso: se um refresh ja usado aparece de novo,
   ele foi roubado — mate a familia inteira de tokens daquela sessao.
3. **Claim de versao**: guarde `versao_sessao` no usuario e no token; incrementar no banco
   invalida tudo daquele usuario. Custa uma leitura, resolve demissao e troca de senha.
4. **Lista de revogados** por `jti` em cache, so ate o token expirar naturalmente.

O caminho 3 e o de melhor retorno para o que voce tem hoje.

### O que ja esta certo
`ValidateLifetime = true`. Vale conferir tambem o `ClockSkew` — o padrao do .NET e **5 minutos**
de tolerancia, o que estende a validade do token sem ninguem perceber. Para janela curta,
`ClockSkew = TimeSpan.Zero`.

### No app mobile
Token no armazenamento seguro do dispositivo, nunca em preferencia comum
([[27-seguranca-do-app-mobile]]).

## Relacionado
- [[09-proteger-cookies-de-sessao]]
- [[10-hash-nas-senhas]]
- [[27-seguranca-do-app-mobile]]
