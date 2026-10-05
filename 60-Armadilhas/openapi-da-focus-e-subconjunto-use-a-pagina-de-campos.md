---
tipo: armadilha
titulo: "O OpenAPI da Focus é um subconjunto: transporte, CEST e DIFAL só existem na página de campos"
projeto: [BTech.NFe.Api]
stack: [focus-nfe, nfe, nfce]
tags: [tipo/armadilha, focus-nfe, contrato, nfe]
palavras-chave: [openapi, campos.focusnfe.com.br, NotaFiscalXML, cest, transportador, volumes, modalidade_frete, nfce, ie_emitente, inscricao_estadual_emitente]
origem: claude-code
criado: 2026-09-29
atualizado: 2026-09-29
confianca: alta
---

# O OpenAPI da Focus é um subconjunto

## Resumo
`emitir_nfe` e `emitir_nfce` no OpenAPI têm poucas dezenas de campos. Não trazem transportadora,
volumes, veículo, CEST nem DIFAL. A lista completa (NF-e **e** NFC-e) é
`https://campos.focusnfe.com.br/nfe/NotaFiscalXML.html`: a tabela é um JSON embutido no HTML, extraível
com regex sobre `{"name":...,"description":...,"type":...,"required":...}`.

## Detalhe
- OpenAPI dá o `required`; a página de campos dá os nomes. Cruzar os dois.
- Achados na NFC-e: `ie_emitente` não existia (é `inscricao_estadual_emitente`) e `modalidade_frete`
  (obrigatório) faltava — toda emissão dava 422. A tela `nfce/nova` mandava `ncm`/`origem`/`csosn`.
- Ver [[focus-ignora-em-silencio-campo-que-nao-reconhece]] e [[validator-espelha-o-required-do-openapi-do-parceiro]].

## Relacionado
- [[BTech.NFe.Api]]
