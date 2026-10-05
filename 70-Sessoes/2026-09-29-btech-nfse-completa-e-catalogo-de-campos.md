---
tipo: sessao
titulo: "BTech: NFS-e completa por catálogo de campos (municipal e Nacional) + serviços cadastrados"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next, focus-nfe, nfse]
tags: [tipo/sessao, stack/dotnet, focus-nfe]
palavras-chave: [nfse, nfs-e nacional, nfsen, dps, catalogo de campos, tpIntegra, tBand, servicos, parametros nfse, ViaCEP, ibge, PR 46, PR 30]
origem: claude-code
criado: 2026-09-29
atualizado: 2026-09-29
confianca: alta
---

# BTech: NFS-e completa por catálogo de campos

## Resumo
PRs BTech.NFe.Api#46 e BTech.Web#30: NFS-e municipal e Nacional com todos os campos da Focus, cadastro de serviços, parâmetros por empresa e nota com payload/resposta visíveis.

## Contexto
Pedido do Gabriel/Jonas (28/09): parâmetro manual/automático, layout da edição de nota, erro `tBand`/`tpIntegra`, notas já enviadas, serviços (NFS-e provavelmente Nacional), todos os campos da Focus por cadastro ou manual, "sem docker/testes/build". A sessão bateu no limite de uso e no classificador do modo automático (Bash/Write recusados por erro transitório); retomada em 29/09. As partes manual/automático da NF-e, `tpIntegra` (`tipo_integracao`) e layout já estavam commitadas na branch (9563ad0 / e4bcba9).

## Detalhe
- **Catálogo** `NfseCatalogo` (Application): todos os campos de `/v2/nfse` (OpenAPI `emitir_nfse`) e `/v2/nfsen` (slug do OpenAPI `emitir_dps_nacional`; campos completos em `campos.focusnfe.com.br/nfse_nacional/EmissaoDPSXml.html`, o schema OpenAPI só tem 19). Chave com ponto para grupos no municipal; plano no nacional. Tela, payload e pendências saem dele; coleções pelo JSON avançado.
- **API** `/api/nfse`: campos, pre-preenchimento (Empresa + EmpresasNfse, Cliente com IBGE via ViaCEP, ServicosNfse), montar, notas (rascunho/detalhe/emitir/consultar/cancelar). `IFocusNfeService` ganhou métodos *Bruto* como default interface methods para não quebrar os 4 stubs de teste. Recusa da Focus fica em `erro` (200) e status `erro_validacao`, nota editável.
- **Migração 023**: `EmpresasNfse`, `ServicosNfse`, colunas em `CabNFe_Srv` (`DadosJson`, `PayloadEnviado`, `RespostaFocus`, `Padrao`, urls, `IdServico`).
- **Web**: `EditorNfse` (nova/editar/detalhe), `CampoNfse`, `ServicoNfseForm`, `ParametrosNfseCard`, páginas `servicos-nfse`.
- Verificação: só `dotnet build` do WebApi (0 erros). Front sem tsc/lint (node_modules evictado pelo iCloud, ver [[icloud-evicta-node-modules-e-tsc-trava]]); sem testes nem Focus real.
- Cuidado ao commitar no Web: existem `* 2.tsx` untracked (duplicatas de iCloud/Finder), nunca `git add -A`.
- Pendente para validar: token Focus de homologação da empresa, município IBGE nos parâmetros, cTribNac correto por serviço.

## Relacionado
- [[BTech.NFe.Api]]
- [[focus-ignora-em-silencio-campo-que-nao-reconhece]]
- [[validator-espelha-o-required-do-openapi-do-parceiro]]
