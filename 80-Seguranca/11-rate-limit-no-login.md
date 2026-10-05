---
tipo: seguranca
titulo: 11. Rate limit no login
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [rate limit, forca bruta, brute force, login, throttle, tentativas, lockout, 429, credential stuffing, autenticacao, entrar, endpoint de login, tela de login]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 11. Rate limit no login
## Resumo
Endpoint de login com limite de tentativas por IP e por conta. Sem isso, forca bruta e so questao de tempo.

## Contexto
Regra 11 das 20 obrigatorias.

## Detalhe

### Duas dimensoes, nao uma
| Limite | Ataque que barra |
| --- | --- |
| por **IP** | forca bruta contra uma conta |
| por **conta** | credential stuffing distribuido (muitos IPs, uma conta) |

So limitar por IP deixa passar botnet. So por conta deixa passar varredura de muitas contas.

### Como esta na [[BTech.NFe.Api]] (referencia)
`AddRateLimiter` com **5 requisicoes por minuto por IP** no login, desligado no ambiente
`Testing` para nao quebrar os testes. Ha teste de integracao dedicado
(`RateLimitIntegrationTests`).

Desligar no ambiente de teste e correto — mas garanta que o teste do proprio rate limit exista,
senao a regra apodrece sem ninguem notar.

### Cuidados
- Resposta `429` com `Retry-After`
- Nao revelar se o usuario existe — [[15-nao-vazar-dados]]
- Limitar tambem: recuperacao de senha, reenvio de e-mail, cadastro. Sao os mesmos vetores.

### Complemento
Rate limit atrasa; nao impede. Some com [[12-bot-protection]] e [[10-hash-nas-senhas]].

## Relacionado
- [[12-bot-protection]]
- [[10-hash-nas-senhas]]
- [[BTech.NFe.Api]]
