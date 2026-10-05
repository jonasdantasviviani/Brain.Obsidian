---
tipo: armadilha
titulo: Modelo do request da Focus NFe usava nomes de campo que não existem na API real
projeto: [BTech.NFe.Api]
stack: [dotnet, focus-nfe, nfe]
tags: [tipo/armadilha, stack/dotnet, dominio/fiscal]
palavras-chave: [focus nfe, emitirnferequest, itemnferequest, nomes de campo, cst_icms, icms_situacao_tributaria, ncm, codigo_ncm, doc.focusnfe.com.br, campos.focusnfe.com.br, homologacao]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: alta
---

# Modelo do request da Focus NFe usava nomes de campo que não existem na API real
## Resumo
`EmitirNfeRequest`/`ItemNfeRequest` (BTech.NFe.Api) tinham `[JsonPropertyName]` com nomes que
parecem plausíveis (`cst_icms`, `csosn`, `origem`, `ncm`, `ie_emitente`, `data_saida_entrada`) mas
**não são os nomes reais da API da Focus** — foram escritos de memória, sem checar
`doc.focusnfe.com.br`. Uma NF-e emitida com esse modelo teria os campos de tributo simplesmente
ignorados pela Focus (ela não reconhece a chave), saindo com ICMS/PIS/COFINS zerados ou a
requisição sendo rejeitada por campo obrigatório ausente.

## Contexto
Descoberto em 2026-09-16 ao construir a ponte real CabNota→Focus (ver
[[BTech.NFe.Api]] e [[2026-09-16-btechplus-frontend-comparacao-telas]], Fase 6). O Jonas pediu
para "retirar os mocks e criar as rotas faltantes" da parte fiscal, comentando que "a Focus tem
documentação bem detalhada" — ou seja, checar a doc real em vez de assumir. Ao montar o mapper
CorNota→ItemNfeRequest, fui confirmar os nomes de campo em `doc.focusnfe.com.br/reference/
emitir_nfe.md` e `campos.focusnfe.com.br/nfe/NotaFiscalXML.html` antes de escrever o mapeamento —
e os nomes reais divergiam do modelo já existente no repositório.

## Detalhe
Divergências encontradas (nome antigo → nome real da Focus):
- Item: `ncm` → `codigo_ncm`
- Item: `origem` → `icms_origem`
- Item: `cst_icms`/`csosn` (dois campos) → `icms_situacao_tributaria` (um campo só, o valor é CST
  ou CSOSN dependendo do regime do emitente)
- Item: `base_calculo_icms`/`aliquota_icms`/`valor_icms` → `icms_base_calculo`/`icms_aliquota`/
  `icms_valor` (prefixo por tributo, não sufixo)
- Item: `cst_pis`/`aliquota_pis`/... → `pis_situacao_tributaria`/`pis_aliquota_porcentual`/...
  (mesmo padrão para `cofins_*`)
- Item: **IPI não existia no modelo** — Focus usa `ipi_situacao_tributaria`, `ipi_base_calculo`,
  `ipi_aliquota`, `ipi_valor`, `ipi_codigo_enquadramento_legal` (esse último obrigatório sempre
  que o IPI é informado; "999" quando não se aplica)
- Header: `ie_emitente`/`ie_destinatario` → `inscricao_estadual_emitente`/
  `inscricao_estadual_destinatario`; `indicador_ie_destinatario` → `indicador_inscricao_estadual_
  destinatario`
- Header: `data_saida_entrada` → `data_entrada_saida` (ordem invertida)
- Header: **faltavam campos obrigatórios** `local_destino` (1/2/3, calculado a partir da UF
  emitente x destinatário), `valor_produtos` e `valor_total` (a Focus não deriva sozinha a partir
  dos itens)

Corrigido reescrevendo `ItemNfeRequest.cs`/`EmitirNfeRequest.cs` com os nomes confirmados na doc,
com comentário no topo do arquivo apontando as duas URLs fonte e a data da checagem. Também achado
de quebra: **nenhum teste cobria os nomes de campo em si** (só a forma HTTP — método, rota, status
code), então essa divergência nunca teria sido pega por CI. `EmitirNfeRequestValidator` ganhou
`RuleForEach` cobrando `CodigoNcm`/`Cfop` por item, o que ao menos garante presença — não garante
correção do valor.

**Ainda não auditado**: `EmitirNfceRequest.cs` (NFC-e) compartilha `ItemNfeRequest` mas não foi
revisado por essa mesma checagem — se a Focus usa nomes diferentes pra NFC-e, o mesmo problema
pode existir lá.

**Lição**: doc de API de terceiro que muda pouco (contratos fiscais tendem a ser estáveis) ainda
assim precisa ser conferida na fonte antes de codificar o modelo — "parece certo" não é o mesmo
que "está na doc". Antes de emitir em produção, **testar no ambiente de homologação da Focus** e
comparar o XML gerado com um exemplo real — isso não substitui revisão de contador/fiscal.

## Relacionado
- [[BTech.NFe.Api]]
- [[camelcase-id-fields-sem-fronteira-de-palavra]]
