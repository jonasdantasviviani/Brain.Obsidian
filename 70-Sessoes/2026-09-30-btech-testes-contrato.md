---
tipo: sessao
titulo: "BTech: frente CONTRATO — snapshot da Focus, webhooks, quebra de OpenAPI e contrato de envio do front"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, xunit, vitest, python, focus-nfe, openapi]
tags: [tipo/sessao, focus-nfe, contrato, testes]
palavras-chave: [contrato, focus nfe, snapshot, campos.focusnfe.com.br, webhook, reentrega, openapi, oasdiff, quebra de contrato, codigo_barras_comercial, documentos_referenciados, sensibilidade]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# BTech: frente CONTRATO

## Resumo
API PR #82 (`test/contrato`) e Web PR #62 (`test/contrato`). Payloads da Focus conferidos contra snapshot oficial versionado, contrato dos webhooks, detector de quebra do nosso OpenAPI e, no front, contrato do que é ENVIADO, relatórios, tipos novos e teste dos testes. Ver [[focus-ignora-em-silencio-campo-que-nao-reconhece]] e [[validator-espelha-o-required-do-openapi-do-parceiro]].

## Detalhe
- **Snapshot da Focus**: `tools/contrato-focus/extrair_snapshot.py` lê `campos.focusnfe.com.br` (o JSON está em `__NEXT_DATA__`; alguns atributos vêm sem `name`, o nome está na descrição `:campo ...`; `enum` às vezes é markdown `+0+: ...`) e o OpenAPI por `doc.focusnfe.com.br/focus-nfe/api-next/v2/branches/2.0/reference/<slug>?reduce=false` (o site em si dá 429; a API interna responde). A página chama a coleção de `itens`, o OpenAPI de `items` (o sistema manda `items`). A `required` da página é grossa (conta condicionais); o teste usa o `required` do OpenAPI + lista curada, cada nome conferido contra o snapshot.
- **Testes .NET**: no projeto Unit (`Contrato/`), com cliente `FocusNfeService` real e `HttpMessageHandler` que captura o corpo. `ContratoAssert.Vazio` lista TODAS as divergências (FluentAssertions mostra só a primeira). Webhooks no projeto Contract via `DispatchProxy` (sem tocar no fake compartilhado).
- **Bugs reais**: `codigo_ean*` do item (é `codigo_barras_*`); 3 campos da NFS-e nacional que só existem em `documentos_referenciados[]`; `WebhookNfePayload.cnpj` (é `cnpj_emitente`). Pendente de conferir em homologação: `numero_protocolo_autorizacao` da NF-e não está no OpenAPI de resposta (protocolo vem em `protocolo_nota_fiscal`; NFC-e: `numero_protocolo`).
- **Detector OpenAPI**: `scripts/openapi_breaking.py` (stdlib) + workflow `contrato-api.yml`; aprovação por rótulo `quebra-de-contrato-aprovada` ou `docs/openapi-quebras-aprovadas.json`. Cuidado que cortou resultado: o controle anti-recursão de `$ref` tem de ser pilha (em curso), não "visitados" global, senão a 2ª operação que usa o mesmo schema some do relatório.
- **Web**: motor de comparação extraído para `helpers/divergencias.ts` (parametrizado pelo contrato) para o `sensibilidade.test.ts` adulterar o contrato em memória. `body: X` e `filtro: X` vêm dos parâmetros da função de `api.ts` (AST). Prova manual: renomear `PedidoItemResponse.tipo` em `contract/openapi.json` derruba `campos.test.ts`.
- **Armadilhas do ambiente**: sed `-i` no macOS exige `''`; o build .NET trata estilo (IDE0290/IDE0300/IDE0005/IDE0007) como erro — use construtor primário e coleção `[]`. O workflow `Seguranca` do Web já falhava na main (npm audit em `@vitest/mocker`), não é desta frente.
- **Não feito**: corpo de requisição das rotas `unknown` (empresa, cliente, produto...) não é conferível no front; NFS-e nacional: obrigatórios de Reforma Tributária da Focus (`ibs_cbs_*`, `consumidor_final`...) seguem fora do catálogo, em lista fechada no teste.

## Relacionado
- [[BTech.NFe.Api]]
- [[2026-09-30-btech-contrato-front-api-e-verificacao-fora-do-icloud]]
