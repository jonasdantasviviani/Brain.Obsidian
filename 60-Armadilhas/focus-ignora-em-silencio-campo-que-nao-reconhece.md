---
tipo: armadilha
titulo: "A Focus NFe ignora em silêncio o campo que não reconhece — payload errado não dá erro"
projeto: [BTech.NFe.Api]
stack: [dotnet, focus-nfe, nfse, nfe]
tags: [tipo/armadilha, focus-nfe, nfse, contrato, json]
palavras-chave: [focus nfe, nfse, nota de servico, json property name, campo ignorado, 422, payload, contrato, openapi, emitir_nfse, endereco tomador]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# A Focus NFe ignora em silêncio o campo que não reconhece

## Resumo
A Focus tem dois comportamentos que, juntos, escondem erro de contrato: **falta de obrigatório** dá
`422` síncrono (visível), mas **nome de campo que não existe** é ignorado **sem erro nenhum**. O
código compila, os testes passam e o documento fiscal sai sem aquele dado.

## Contexto
Segunda ocorrência no [[BTech.NFe.Api]]. A primeira foi o `EmitirNfeRequest`
([[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]]); em 18/09/2026 o
`EmitirNfseRequest` tinha os mesmos vícios, e pior — os dois tipos de erro ao mesmo tempo.

## Detalhe

### O que estava errado na NFS-e
Obrigatórios ausentes (dariam 422 em **toda** emissão): `natureza_operacao`,
`optante_simples_nacional`, `servico.codigo_municipio`; `prestador.inscricao_municipal` opcional e
`tomador` opcional, ambos obrigatórios na Focus.

Ignorados em silêncio — os piores:
- **`tomador.endereco` é objeto aninhado.** O modelo tinha `logradouro`, `numero`, `bairro`, `cep`
  soltos no tomador. A nota sairia sem endereço nenhum, sem um único erro.
- **`valor_deducao_desconto_incondicionado`** não existe. O nome real é `desconto_incondicionado`.

### Como conferir o contrato de verdade
A página de referência é readme.io e serve o OpenAPI por uma API interna. Dá para baixar:

```bash
curl -sS -H 'accept: application/json' \
  'https://doc.focusnfe.com.br/focus-nfe/api-next/v2/branches/2.0/reference/emitir_nfse?reduce=false' \
  | python3 -c "import json,sys; print(json.dumps(json.load(sys.stdin)['data']['api']['schema']['components']['schemas'], indent=2, ensure_ascii=False))"
```

Troque o slug (`emitir_nfe`, `emitir_cte`, `emitir_mdfe`…). O `required` de cada schema é
exatamente a lista que o validator precisa cobrir. **Isso resolve a classe inteira do problema** —
não confira por leitura da página, baixe o schema.

### A defesa que ficou no código
Um `AbstractValidator` por documento, cobrindo o `required` do OpenAPI: transforma o 422 mudo da
Focus (que nem sempre diz o campo) num 400 nosso que aponta. Ver
[[validator-espelha-o-required-do-openapi-do-parceiro]].

## Como evitar
Antes de criar ou editar qualquer `Domain/Models/Focus/*`, baixe o schema. Um `JsonPropertyName`
errado não é pego por compilador, por teste unitário nem por revisão de código — só pela nota
saindo errada semanas depois.

## Relacionado
- [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]]
- [[validator-espelha-o-required-do-openapi-do-parceiro]]
- [[BTech.NFe.Api]]
