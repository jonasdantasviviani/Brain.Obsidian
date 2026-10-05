---
tipo: sessao
titulo: Oito regras de seguranca novas (21 a 28)
projeto: [Cerebro, BTech.NFe.Api, Heavy, ICook]
stack: [seguranca, python]
tags: [tipo/sessao, seguranca]
palavras-chave: [seguranca, regras, 21, 28, ssrf, webhook, cors, swagger, auditoria, sessao, mobile, backup, lacuna, owasp]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Oito regras de seguranca novas (21 a 28)
## Resumo
As 20 regras viraram 28, escolhidas por lacuna real nos projetos do Jonas — nao por lista generica de OWASP.

## Contexto
07/09/2026. Pergunta do Jonas: "existe mais regras de seguranca importante de add no cerebro?"
Em vez de despejar uma lista, fui ao codigo procurar o que as 20 nao cobriam.

## Detalhe

### As oito novas, e o que ancorou cada uma

| Regra | O que a justificou no codigo |
| --- | --- |
| **21** nao expor docs em producao | `UseSwagger()` sem guarda de ambiente na [[BTech.NFe.Api]] |
| **22** CORS restritivo | ja esta certo — a nota trava o padrao antes que alguem "conserte" um erro de CORS com `SetIsOriginAllowed(_ => true)` |
| **23** prevenir SSRF | integracao Focus NFe e mapas; hoje as URLs vem de configuracao (certo), mas "importar de uma URL" aparece cedo ou tarde |
| **24** webhooks seguros | existe `FocusNfeWebhookController` em producao |
| **25** ciclo de vida da sessao | JWT de 8h com `ValidateLifetime`, **sem revogacao** |
| **26** trilha de auditoria | nenhum projeto tem; o [[Heavy]] precisa por requisito de produto (disputa de canhoto, defesa fiscal) |
| **27** seguranca do app mobile | dois apps Flutter; o do Heavy ainda sem `flutter_secure_storage` |
| **28** backup testado e protegido | a [[BTech.NFe.Api]] ja lida com `.bak` e tem rota de validacao |

### O que encontraram na primeira passada
- **21**: Swagger publico em producao na BTech; `MapOpenApi` sem guarda no Heavy
- **24**: o webhook da NF-e tem `[Authorize]` mas **nao tem assinatura HMAC nem anti-replay**
- **25**: logout nao invalida token na BTech; ICook sem `ValidateLifetime`
- **26**: sem trilha de auditoria em lugar nenhum
- **27**: app do Heavy sem armazenamento seguro
- **28**: backup sem teste de restauracao visivel

### Descoberta lateral
A [[BTech.NFe.Api]] envia `X-XSS-Protection: 1; mode=block`. O header e **obsoleto e
desaconselhado** — navegadores modernos removeram o filtro e em versoes antigas ele
*introduzia* vulnerabilidade. Quem faz esse trabalho hoje e a CSP.

### O que considerei e NAO adicionei, com motivo
- **CSRF**: coberto pela regra 09 enquanto a sessao for Bearer token. Vira regra propria no dia
  em que migrarem para cookie.
- **Container como root**: os tres Dockerfiles ja tem `USER`. Sem lacuna.
- **Desserializacao insegura**: `System.Text.Json` nao tem o problema do `BinaryFormatter`.
- **LGPD (retencao, minimizacao, exclusao)**: importante, mas se sobrepoe a regra 05. Melhor
  aprofundar a 05 do que criar uma regra que repete.
- **Idempotencia em operacao fiscal**: emitir NF-e duas vezes e problema serio, mas e
  confiabilidade, nao seguranca. Vale nota propria em [[20-Padroes]].

### Calibragem do auditor
A checagem 24 dava **falso positivo**: procurava `HMAC|ComputeHash` em qualquer lugar do
repositorio e dava "ok" mesmo com o handler sem assinatura nenhuma. Corrigida para procurar
**dentro do arquivo que recebe o webhook**. Terceira vez nesta serie que uma checagem precisou
ser estreitada — ver [[auditoria-por-evidencia-em-vez-de-sast]].

## Relacionado
- [[Checklist-Seguranca]]
- [[auditoria-por-evidencia-em-vez-de-sast]]
- [[2026-09-07-20-regras-de-seguranca]]
