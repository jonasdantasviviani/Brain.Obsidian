---
tipo: padrao
titulo: OpenAPI gerado do codigo, nunca escrito a mao
projeto: [Heavy]
stack: [dotnet, typescript, dart, openapi]
tags: [tipo/padrao, stack/dotnet, cerebro/padrao-obrigatorio]
palavras-chave: [openapi, swagger, contrato, api, cliente gerado, typescript, dart, codegen, dto]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# OpenAPI gerado do codigo, nunca escrito a mao
## Resumo
O contrato nasce do C# e gera os clientes TypeScript e Dart no build; mudanca de contrato quebra a compilacao das duas pontas de proposito.

## Contexto
Regra do [[Heavy]] (doc 19, §1). Sao **tres documentos**, um por grupo de consumidores:
`integracao`, `app`, `painel`.

## Detalhe

```text
dotnet build ──► openapi.json ──┬──► cliente TypeScript (painel)
                                └──► cliente Dart (app)
```

### Consequencias praticas
- Mudanca de contrato no C# **quebra a compilacao** do painel e do app. Esse e o objetivo:
  substitui o tipo compartilhado que um monorepo poliglota nao tem.
- As pastas de cliente gerado sao **regeneradas e nao versionadas**:
  `painel/src/infrastructure/api/gerado/` e `app/lib/infrastructure/api/gerado/`.
  Nunca editar, nunca importar nada delas fora da camada `infrastructure`.
- Escrever um `openapi.yaml` a mao, ou digitar um DTO no front espelhando o back, e
  **exatamente o erro que essa regra existe para impedir**.

## Relacionado
- [[Heavy]]
- [[arquitetura-dotnet-em-camadas]]
