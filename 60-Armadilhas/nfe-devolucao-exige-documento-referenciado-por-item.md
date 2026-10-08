---
tipo: armadilha
titulo: SEFAZ rejeita devolução sem documento referenciado por item (DFeReferenciado)
projeto: [BTech.NFe.Api]
stack: [dotnet, focus-nfe]
tags: [tipo/armadilha, dominio/fiscal]
palavras-chave: [devolução, finalidade 4, dfe referenciado, nItem, chave_acesso_dfe_referenciado, numero_item_dfe_referenciado, rejeição]
origem: claude-code
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---

## Resumo
Finalidade 4 exige em CADA item a chave da nota devolvida + nItem dela; só `notas_referenciadas` não basta.

## Detalhe
Rejeição: "NF-e de devolução de mercadoria não possui documento fiscal referenciado por item [nItem: 1]". Campos Focus:
`items[].chave_acesso_dfe_referenciado` e `items[].numero_item_dfe_referenciado`. O nItem vem da posição do produto na original
(achada por chave); sem ela, ordem dos itens + aviso. Correção no PR BTech.NFe.Api#89. Ver [[cfop-de-entrada-em-nota-de-saida-na-devolucao]], [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]].
