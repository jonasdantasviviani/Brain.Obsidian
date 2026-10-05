---
tipo: padrao
titulo: PUT parcial sobre o registro existente (em vez de gravar a entidade do corpo)
projeto: [BTech.NFe.Api]
stack: [dotnet, aspnetcore, efcore, fluentvalidation]
tags: [tipo/padrao, stack/dotnet, seguranca/multitenant]
palavras-chave: [put, patch, atualizacao parcial, mass assignment, multi-tenant, IDOR, JsonElement, ValidationProblemDetails, 400, perda de dados, ServiceBase, global query filter]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# PUT parcial sobre o registro existente
## Resumo
Controller de CRUD que faz `[FromBody] Entidade` + `DbSet.Update()` apaga campo que o formulário não manda, deixa gravar em linha de outro tenant e devolve 400 sem dizer qual campo faltou. O PUT tem que carregar o registro pela consulta filtrada e aplicar só as chaves presentes no JSON.

## Contexto
[[BTech.NFe.Api]], 2026-09-11: 28 endpoints de PUT trocados de uma vez.

## Detalhe

### Os três problemas do PUT que grava a entidade do corpo
1. **Perda de dados**: a tela manda 10 campos, a entidade tem 80 — o resto vai a default.
   Editar usuário apagava a senha; editar empresa apagava numeração fiscal e certificado.
2. **400 sem explicação**: com `<Nullable>enable</Nullable>`, o MVC trata toda string
   não-anulável como obrigatória. Coluna legada NOT NULL com DEFAULT no banco (`TipoReciboDanfe`,
   `Bloquaedo`) vira campo obrigatório que nenhuma tela conhece. Ver
   [[nullable-enable-transforma-coluna-legada-em-campo-obrigatorio]].
3. **Linha de outro tenant**: `DbSet.Update(entidade)` anexa pela chave sem ler a linha; o
   global query filter não participa. PUT com id alheio grava por cima (IDOR).

### Forma
```csharp
public async Task<ActionResult<Empresa>> Update(int id, [FromBody] JsonElement corpo, CancellationToken ct = default) =>
    await this.AtualizarParcialAsync(
        await _service.GetByIdAsync(id, ct),      // consulta já filtrada por tenant → 404
        corpo,
        entidade => _service.UpdateAsync(entidade, ct),
        [nameof(Empresa.CodEmpresa)]);            // chaves: só podem repetir o valor da rota
```
O helper (`AtualizacaoParcial.cs`, camada WebApi):
- percorre `corpo.EnumerateObject()` e casa com propriedade **escalar** gravável (case-insensitive);
  navegação e coleção ficam de fora;
- ignora chaves, `IdTenant` e campos protegidos por regra (ex.: `AdminSistema`, `SuperUsuario`);
- `null` em propriedade não-anulável (`NullabilityInfoContext`) vira erro de campo, não 500;
- valor de tipo errado → `ModelState` com o nome do campo, em vez de exceção;
- roda o `IValidator<T>` do FluentValidation **depois** do merge e devolve `ValidationProblem`.

### Semântica que sai disso
- Campo ausente = não mexe. Campo `null` = apaga (quando a coluna aceita).
- `PUT /api/usuarios/{id}` sem `senha` mantém a senha atual.
- Chave divergente continua `400 Id divergente.` (contrato antigo preservado).

## Relacionado
- [[BTech.NFe.Api]]
- [[nullable-enable-transforma-coluna-legada-em-campo-obrigatorio]]
- [[arquitetura-dotnet-em-camadas]]
