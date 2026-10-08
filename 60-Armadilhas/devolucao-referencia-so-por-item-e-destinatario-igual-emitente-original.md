---
tipo: armadilha
titulo: Devolução NF-e: referência só por item (1010), destinatário = emitente original (1194), chave vem com prefixo NFe
projeto: [BTech.NFe.Api]
stack: [dotnet, focus-nfe]
tags: [tipo/armadilha, dominio/fiscal]
palavras-chave: [devolução, 1010, 1194, 656, dfe referenciado, notas_referenciadas, prefixo NFe, homologação]
origem: claude-code
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---

## Resumo
Descobertas na SEFAZ de homologação (testes `BTech.NFe.Tests.FocusHml`), não dá para saber só pela doc.

## Detalhe
- Referenciar a nota no nível da nota (`notas_referenciadas`) E no item (`chave_acesso_dfe_referenciado`/`numero_item_dfe_referenciado`): rejeição **1010**. Só por item.
- Devolução de saída: destinatário tem de ser o emitente da nota referenciada (**1194**) — quem devolve é o comprador (precisa de 2 emitentes no teste). Devolução de entrada (tipo_documento 0, CFOP 1.202) referenciando a própria venda autoriza.
- A Focus devolve `chave_nfe` como `NFe`+44 dígitos; buscar com e sem prefixo.
- **656 "Consumo Indevido"**: bloqueio de ~1h por rejeições repetidas no mesmo emitente; ter vários emitentes e trocar.
- 696: destinatário não contribuinte exige consumidor_final=1; 232: CNPJ com inscrição exige IE; chave inventada = 547 (dígito verificador).
Relacionado: [[nfe-devolucao-exige-documento-referenciado-por-item]], [[ref-focus-igual-sequencia-da-nota-e-busca-por-tenant]], [[BTech.NFe.Api]].
