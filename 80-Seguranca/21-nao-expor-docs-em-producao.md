---
tipo: seguranca
titulo: 21. Nao expor documentacao e debug em producao
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [swagger, openapi, scalar, documentacao, producao, debug, endpoint, exposicao, superficie, deploy, publicar]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 21. Nao expor documentacao e debug em producao
## Resumo
Swagger, Scalar, health detalhado e endpoints de debug ficam atras de ambiente ou de autenticacao — nunca abertos em producao.

## Contexto
Regra 21. **Achado real:** na [[BTech.NFe.Api]], `app.UseSwagger()` e `app.UseSwaggerUI()`
estao no inicio do pipeline **sem nenhuma condicao de ambiente** — a documentacao completa da
API fica publica em producao.

## Detalhe

### Por que importa
Swagger aberto entrega de graca: todos os endpoints, todos os parametros, todos os formatos de
payload e as versoes da API. Nao e vulnerabilidade por si, e **reconhecimento gratuito** —
transforma horas de tentativa e erro num arquivo JSON.

Pior no caso de uma API fiscal, onde os nomes dos endpoints ja dizem o que cada um movimenta.

### A correcao
```csharp
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(/* ... */);
}
```

Se a documentacao precisa existir em homologacao ou producao, coloque atras de autenticacao —
nunca simplesmente aberta.

O [[Heavy]] ja faz certo: `app.MapScalarApiReference()` esta dentro de
`if (app.Environment.IsDevelopment())`. Use ele como referencia.

### Vale para o mesmo grupo
- `/health` detalhado (que lista dependencias e versoes) — separe `live`/`ready` publicos de um
  health detalhado autenticado. O Heavy ja separa.
- endpoints de administracao e de importacao
- `UseDeveloperExceptionPage` ([[15-nao-vazar-dados]])
- headers que anunciam versao: `Server`, `X-Powered-By`, `X-AspNet-Version`

### Detalhe que passa batido
`X-XSS-Protection` esta **obsoleto** e desaconselhado — os navegadores modernos removeram o
filtro, e em versoes antigas ele **introduzia** vulnerabilidade. A [[BTech.NFe.Api]] ainda envia
`X-XSS-Protection: 1; mode=block`. Remova; quem faz esse trabalho hoje e a CSP
([[18-security-headers]]).

## Relacionado
- [[15-nao-vazar-dados]]
- [[18-security-headers]]
- [[BTech.NFe.Api]]
