---
tipo: seguranca
titulo: 24. Webhooks seguros (assinatura, idempotencia, replay)
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [webhook, callback, assinatura, hmac, idempotencia, replay, focus nfe, notificacao, integracao, endpoint publico]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 24. Webhooks seguros (assinatura, idempotencia, replay)
## Resumo
Webhook e endpoint que qualquer um na internet pode chamar: verifique assinatura, rejeite replay e trate reentrega como normal.

## Contexto
Regra 24. A [[BTech.NFe.Api]] **tem um webhook em producao**:
`FocusNfeWebhookController` em `POST /api/nfe/webhook`, que atualiza o status da nota fiscal.

## Detalhe

### Como esta hoje
O controller tem `[Authorize]`, o que ja e melhor que a maioria — o endpoint nao esta aberto.
Mas vale conferir duas coisas:

1. **A Focus NFe consegue mandar um JWT valido?** Se nao, o webhook nunca chega e o status da
   nota so atualiza por polling. Vale confirmar que chamadas reais estao passando.
2. Se a autenticacao e por um token fixo criado para a Focus, ele **fica guardado no painel
   deles** — vaza junto se houver incidente la, e nao expira.

### O padrao para webhook
**Assinatura HMAC do corpo**, comparada em tempo constante:
```csharp
var assinatura = Request.Headers["X-Signature"].ToString();
var esperado = Convert.ToHexString(
    HMACSHA256.HashData(Encoding.UTF8.GetBytes(segredo), corpoBruto));
if (!CryptographicOperations.FixedTimeEquals(
        Encoding.UTF8.GetBytes(assinatura), Encoding.UTF8.GetBytes(esperado)))
    return Unauthorized();
```
Detalhe que quebra na pratica: assine o **corpo bruto**, antes de qualquer desserializacao.
Reserializar muda espacos e ordem, e a assinatura nunca bate.

**Timestamp + janela** contra replay: rejeite o que tem mais de ~5 minutos, e guarde os ids
processados para nao aceitar o mesmo duas vezes.

**Idempotencia**: provedores reenviam quando nao recebem 200. Processar duas vezes o
"nota autorizada" nao pode gerar efeito duplicado. Guarde o id do evento e ignore repetido.

**Responda rapido**, enfileirando o trabalho pesado. Provedor que espera muito considera falha e
reenvia — e ai a idempotencia vira obrigatoria de novo.

### O impacto aqui e fiscal
Sem verificacao, alguem que descubra o endpoint pode forjar `status=autorizado` com uma chave
inventada. O sistema passa a acreditar que uma nota foi autorizada quando nao foi.

## Relacionado
- [[23-prevenir-ssrf]]
- [[06-auth-no-servidor]]
- [[BTech.NFe.Api]]
