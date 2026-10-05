---
tipo: armadilha
titulo: Devolução rejeitada "CFOP de entrada para NF-e de saída" — natureza 1201/1202 com movimento Saída
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next, focus-nfe]
tags: [tipo/armadilha, dominio/fiscal]
palavras-chave: [devolução, cfop, 1202, 1201, 5202, entrada, saída, natureza de operação, sefaz]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

# CFOP de entrada em nota de saída (devolução)

CFOP 1/2/3 = entrada, 5/6/7 = saída; a SEFAZ recusa o cruzamento. Na tela de devolução o movimento padrão era Saída e a lista
de naturezas **caía para "todas"** quando nenhuma casava com o sentido — o usuário via 1.201/1.202 (entrada) numa nota de saída.

## Correção
- Web: `src/lib/naturezaOperacao.ts` (sentido, explicação e filtro por sentido, sem fallback); escolha explicada "cliente devolveu" (E, 1.2xx/2.2xx) × "estou devolvendo" (S, 5.2xx/6.2xx); a natureza escolhida acerta o movimento.
- API: conferência fiscal barra CFOP × movimento com mensagem do que trocar.
- Natureza de saída (5.202/6.202) precisa existir no cadastro do tenant; a tela avisa quando falta.

Ver [[BTech.NFe.Api]].
