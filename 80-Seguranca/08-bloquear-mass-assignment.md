---
tipo: seguranca
titulo: 08. Bloquear mass assignment
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [mass assignment, over posting, dto, frombody, binding, entidade, payload, campo protegido, endpoint, put, criar, salvar, receber json, corpo da requisicao, formulario, cadastrar, controller, request]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 08. Bloquear mass assignment
## Resumo
Endpoint nunca recebe a entidade de dominio direto: recebe um DTO com exatamente os campos que o cliente pode escrever.

## Contexto
Regra 8 das 20 obrigatorias.

## Detalhe

### O ataque
```csharp
// ERRADO
[HttpPost] public async Task<IActionResult> Criar([FromBody] Usuario usuario)
```
O cliente manda `{"nome":"x","perfil":"admin","empresaId":99}` e o binder preenche tudo.
Campos que nem aparecem na sua tela viram vetor de escalonamento de privilegio.

```csharp
// CERTO
public sealed record CriarUsuarioRequest(string Nome, string Email);
[HttpPost] public async Task<IActionResult> Criar([FromBody] CriarUsuarioRequest req)
```

### O que a auditoria procura
`[FromBody]` com tipo cujo nome **nao** termina em `Dto`, `Request`, `Command`, `Input`,
`Model` ou `Payload`.

### No seu codigo hoje — achado real
A [[BTech.NFe.Api]] recebe entidades EF direto em varios controllers:
`AliquotasIcmController`, `CabEntradaController`, `CabNotaController`, `CabPedidoController` e
outros. Como o modelo e scaffolded do banco, **a entidade tem todas as colunas da tabela** —
inclusive as que o cliente jamais deveria escrever.
Vale um passe de refatoracao criando `*Request` por endpoint de escrita.
Isso conversa com [[17-trim-nas-respostas-de-api]], que e o mesmo problema na direcao da saida.

## Relacionado
- [[17-trim-nas-respostas-de-api]]
- [[14-validacao-dos-inputs]]
- [[BTech.NFe.Api]]
