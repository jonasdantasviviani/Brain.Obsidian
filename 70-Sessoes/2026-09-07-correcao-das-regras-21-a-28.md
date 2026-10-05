---
tipo: sessao
titulo: Correcao das pendencias das regras 21 a 28
projeto: [BTech.NFe.Api, Heavy]
stack: [dotnet, seguranca]
tags: [tipo/sessao, stack/dotnet, seguranca]
palavras-chave: [seguranca, webhook, swagger, clockskew, focus nfe, correcao, teste, forja, producao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Correcao das pendencias das regras 21 a 28
## Resumo
Swagger fechado em producao nos dois backends e o webhook da NF-e deixou de confiar no proprio payload.

## Contexto
07/09/2026, seguindo [[2026-09-07-oito-regras-de-seguranca-novas]].

## Detalhe

### A correcao que mais vale — regra 24
A documentacao da Focus NFe **nao descreve assinatura de webhook**: so "JSON via POST para uma
URL que voce define", com reenvio em 1 min, 30 min, 1 h, 3 h e 24 h. Sem HMAC, nao ha como
provar pelo payload que a chamada veio deles.

Entao a defesa nao e verificar assinatura — e **parar de confiar no corpo**:

```csharp
// A fonte da verdade e a consulta autenticada, nunca o corpo do webhook.
consulta = await _focusNfeService.ConsultarAsync(payload.Ref, false, cancellationToken);
await _cabNotaService.AtualizarStatusFocusPorRefAsync(
    payload.Ref, consulta.Status, consulta.ChaveNfe, cancellationToken);
```

Quem forjar `status=autorizada` nao consegue nada: o valor gravado e o que a consulta devolveu.
Falha na consulta responde **502**, e a Focus reenvia — melhor que perder a notificacao.

### Regra 21 — nos dois backends
- [[BTech.NFe.Api]]: `UseSwagger`/`UseSwaggerUI` dentro de `if (!app.Environment.IsProduction())`
- [[Heavy]]: `MapOpenApi(...).AllowAnonymous()` idem. Os clientes TypeScript e Dart sao gerados
  no **build**, a partir do arquivo em disco — a rota em runtime nao servia a nada em producao.
- Removido `X-XSS-Protection`, que e obsoleto e ja foi vetor de vulnerabilidade.

### Regra 25 — parcial, e assumido
`ClockSkew = TimeSpan.Zero` no JWT: o padrao do .NET e **5 minutos** de tolerancia, que estendem
a validade sem ninguem perceber. Revogacao de verdade **nao** foi feita — exige coluna nova, e a
[[BTech.NFe.Api]] evolui schema por script SQL ([[migrations-sql-manuais-em-vez-de-ef-migrations]]).
Ficou registrado como lacuna conhecida no `CLAUDE.md` do repo.

### O teste que quase nasceu inutil
O primeiro teste que escrevi para a forja verificava um campo do proprio dublê — passaria mesmo
se o controller gravasse o payload forjado. Reescrito para **semear a nota e ler o banco** depois
da chamada, com `IgnoreQueryFilters()` porque o filtro multi-inquilino escondia a linha da
leitura de conferencia.

Um teste de seguranca que nao testa a seguranca e pior que nenhum: da confianca sem dar protecao.

### Verificacao
- `dotnet build` nos dois: 0 avisos (Heavy com `TreatWarningsAsErrors`)
- **602 testes verdes**: 392 unitarios + 182 de contrato + 28 funcionais, 0 falhas
  (os funcionais importam aqui: cobrem os fluxos de autenticacao, que o
  `ClockSkew = TimeSpan.Zero` poderia ter quebrado)
- 8 testes do webhook, incluindo forja de status e falha de consulta

### Descoberta do dia
O auditor deu tres "ok" falsos porque casava em **comentario** —
ver [[auditor-que-casa-em-comentario]].

## Relacionado
- [[24-webhooks-seguros]]
- [[21-nao-expor-docs-em-producao]]
- [[auditor-que-casa-em-comentario]]
- [[pendencias-de-seguranca-que-exigem-decisao]]
