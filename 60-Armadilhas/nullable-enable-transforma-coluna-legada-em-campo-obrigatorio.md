---
tipo: armadilha
titulo: Nullable enable transforma coluna legada em campo obrigatório (400 sem nome de campo)
projeto: [BTech.NFe.Api]
stack: [dotnet, aspnetcore, efcore]
tags: [tipo/armadilha, stack/dotnet]
palavras-chave: [nullable enable, null!, implicit required, One or more validation errors occurred, 400, scaffold, DEFAULT, IDENTITY, CodEmpresa, ValueGeneratedNever, Required properties are missing]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Nullable enable transforma coluna legada em campo obrigatório
## Resumo
Entidade scaffoldada tem `public string X { get; set; } = null!;` para coluna NOT NULL. Com `<Nullable>enable</Nullable>` o MVC põe um Required implícito nela: o formulário que não conhece o campo leva "One or more validation errors occurred." sem saber qual campo é.

## Contexto
[[BTech.NFe.Api]], 2026-09-11: cadastro de empresa e de usuário quebrados no teste do Jonas.

## Detalhe
- A validação roda sobre o **valor** da propriedade depois do binding. Logo, basta a entidade
  nascer com o valor: `= "R"` em vez de `= null!` resolve o 400 **e** o NOT NULL no banco.
- Onde colocar sem perder no re-scaffold: arquivo parcial próprio com construtor —
  `Empresa.Padroes.cs` com `public Empresa() { TipoReciboDanfe = "R"; ... }`. Inicializador de
  propriedade roda antes do corpo do construtor, então o construtor vence o `null!`.
- Os valores saem do próprio schema: `grep "ADD  DEFAULT" migrations/000_schema_base.sql`.
- Parente disso: propriedade `bool?` mapeada com `.IsRequired()` no DbContext →
  `SaveChanges` estoura "Required properties '{...}' are missing" (o InMemory reclama sempre).
  Mesma correção: valor no construtor.
- Outra do mesmo banco legado: **nem toda PK é IDENTITY**. `Empresas.CodEmpresa` e
  `CabPedidos.NumeroPedido` não são — sem a aplicação gerar o próximo código, toda empresa
  nascia com código 0, a segunda estourava a PK e a tela achava que estava criando de novo
  (usa o código para saber se é edição). Conferir com:
  `awk '/CREATE TABLE \[dbo\]\.\[Empresas\]\(/,/CONSTRAINT/' migrations/000_schema_base.sql`.

## Relacionado
- [[BTech.NFe.Api]]
- [[put-parcial-sobre-o-registro-existente]]
