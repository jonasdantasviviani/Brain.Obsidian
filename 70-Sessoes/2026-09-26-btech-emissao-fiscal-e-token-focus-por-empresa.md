---
tipo: sessao
titulo: "BTech — emissão fiscal funcionando, token Focus por empresa, cadastros e sidebar"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, efcore, sqlserver, focus-nfe, next, react]
tags: [tipo/sessao, fiscal, focus-nfe, multi-tenant, ui]
palavras-chave: [emitir nota, consultar status, sefaz, token focus, empresa, sidebar, busca clientes, codigo sequencial, estoque codigo, certificado]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-26
confianca: alta
---

# BTech — emissão fiscal funcionando, token Focus por empresa, cadastros e sidebar

## Resumo
Emissão/consulta de NF-e destravadas, token Focus por empresa, código sequencial por tenant, busca no servidor e sidebar corrigida — PRs Api#43 e Web#24.

## Pedido
Sidebar com scrollbar/um menu por vez/submenu inacessível recolhida; busca de clientes só na
página; código 1093796 em vez de 70; tirar certificado; "Empresa 1 não encontrada"; estoque sem
código; emitir uma nota sem lote. Depois: "foco nos dados fiscais, envio, consulta — nada
funciona" e "token Focus é por empresa e por ambiente".

## Causas reais
- Consulta/emissão: [[ef-update-de-entidade-sem-rastreamento-com-a-mesma-ja-rastreada]].
- Deslogava ao emitir: [[401-de-servico-externo-repassado-desloga-o-usuario]].
- Código pulando: [[identity-e-contador-da-tabela-inteira-em-multi-tenant]].
- "Emitir Nota" só salvava rascunho; consultar exigia chave (só existe após autorizar).
- Submenu recolhido: `overflow-y-auto` no `<nav>` força recorte no eixo x → flyout virou
  `position: fixed`. Ver [[flyout-de-menu-fecha-ao-atravessar-o-vao]].
- Nota com empresa inexistente (importador antigo gravava código 0) → usa a única empresa.

## Entregue
Decisão [[focus-token-por-empresa-cifrado]]. PRs:
B-Tech-Sistemas/BTech.NFe.Api#43 e B-Tech-Sistemas/BTech.Web#24 (mergear juntos).
Testes: 556 unit, 211 contract, 42 functional, 61 integração (SQL Server). API validada contra a
Focus de homologação com token falso.

## Ambiente
- iCloud evicta `src/`, `tests/` e `node_modules` — `brctl download` e `tsc/eslint/next build`
  numa cópia no scratchpad. Ver [[icloud-evicta-node-modules-e-tsc-trava]].
- Branches anteriores (#41/#22) já mergeadas por squash: trabalho movido para branch nova
  ([[branch-mergeada-por-squash-e-apagada-no-remoto]]).
- Banco local estava sem 016–020; aplicadas via sqlcmd.
- Arquivos "* 2.tsx" duplicados do iCloud no Web — não versionados, ignorados.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[focus-token-por-empresa-cifrado]]
- [[ef-update-de-entidade-sem-rastreamento-com-a-mesma-ja-rastreada]]
- [[401-de-servico-externo-repassado-desloga-o-usuario]]
- [[identity-e-contador-da-tabela-inteira-em-multi-tenant]]
- [[flyout-de-menu-fecha-ao-atravessar-o-vao]]
- [[icloud-evicta-node-modules-e-tsc-trava]]
