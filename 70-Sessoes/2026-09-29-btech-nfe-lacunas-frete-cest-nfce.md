---
tipo: sessao
titulo: "NF-e: frete/transportadora, CEST e auditoria da NFC-e"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, nextjs, focus-nfe]
tags: [tipo/sessao, fiscal]
palavras-chave: [frete, transportadora, cest, nfce, auditoria, ProdutosCest, migracao 026]
origem: claude-code
criado: 2026-09-29
atualizado: 2026-09-29
confianca: media
---

# NF-e: frete/transportadora, CEST e auditoria da NFC-e

## O que foi feito
- PRs: API #51 e Web #34 (branch `feat/nfe-frete-cest-nfce-auditoria`).
- Frete: `CabNotas` já tinha `FretePorConta`, `IDTransporte`, `PlacaVeiculo`, `Transp*`; mapeado para os campos
  `*_transportador`, `veiculo_*`, `volumes[]`. Transportadora = `Fornecedores` (`TipoForne='T'`) — **não confirmado em base real**.
- CEST: `ClFiscal.CodCEST` (por NCM) já existia e é o fallback; `ProdutosCest` (migração 026) é por produto. Exigido só com ICMS-ST.
- NFC-e: `ie_emitente` errado, `modalidade_frete` faltando, validador novo. Ver [[openapi-da-focus-e-subconjunto-use-a-pagina-de-campos]].
- DIFAL/II: não implementados (dados e decisão fiscal pendentes; descrito no PR).

## Pendente
- Testar contra a Focus (homologação) e conferir os dois pontos não confirmados em `.bak` real.
- Front sem tsc/build (worktree sem node_modules).
