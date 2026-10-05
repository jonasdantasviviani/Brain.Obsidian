---
tipo: padrao
titulo: Validator espelha o required do OpenAPI do parceiro
projeto: [BTech.NFe.Api]
stack: [dotnet, fluentvalidation, focus-nfe]
tags: [tipo/padrao, validacao, integracao, api-externa]
palavras-chave: [fluentvalidation, validator, openapi, required, api externa, 422, 400, focus nfe, contrato]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Validator espelha o required do OpenAPI do parceiro

## Resumo
Para cada payload que sai para uma API externa, um `AbstractValidator` com uma regra por campo
`required` do OpenAPI **dela**. O erro vira um 400 nosso que aponta o campo, em vez de um 422 do
parceiro que não aponta.

## Contexto
A Focus NFe recusa a emissão de forma síncrona quando falta obrigatório, mas a mensagem raramente
diz **qual** campo — e o usuário final vê um erro que não o ajuda a corrigir nada. Ver
[[focus-ignora-em-silencio-campo-que-nao-reconhece]].

## Detalhe

### A regra
O `required` do schema do parceiro é a lista. Nada além disso entra:

```csharp
RuleFor(x => x.Prestador.InscricaoMunicipal).NotEmpty()
    .WithMessage("Inscrição municipal do prestador é obrigatória na NFS-e.");
RuleFor(x => x.Servico.CodigoMunicipio).NotEmpty().Length(7)
    .WithMessage("Código IBGE (7 dígitos) do município de prestação do serviço é obrigatório.");
```

### O que NÃO entra
Regra que varia por cliente do parceiro — no caso da NFS-e, exigência de prefeitura (campo extra,
limite menor de discriminação). Isso muda por município e só aparece no retorno da própria Focus;
codificar aqui dá falso negativo e bloqueia emissão válida.

### Mensagem
Diz o campo **e a unidade esperada**: "código IBGE de 7 dígitos", "14 dígitos, sem máscara".
Quem lê é quem está preenchendo a tela, não quem escreveu a integração.

### Condicionais do contrato também entram
Quando o parceiro diz "obrigatório se X informado", vira `When`:

```csharp
RuleFor(x => x.SerieRpsSubstituido).NotEmpty()
    .When(x => !string.IsNullOrWhiteSpace(x.NumeroRpsSubstituido));
```

## Relacionado
- [[focus-ignora-em-silencio-campo-que-nao-reconhece]]
- [[BTech.NFe.Api]]
