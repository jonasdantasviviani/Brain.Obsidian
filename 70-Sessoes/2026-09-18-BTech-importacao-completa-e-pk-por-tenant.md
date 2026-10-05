---
tipo: sessao
titulo: "BTech — importação das 77 tabelas, PK por tenant e detalhe no painel"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, efcore, sqlserver, next]
tags: [tipo/sessao, importador, multi-tenant, migracao]
palavras-chave: [importacao, 77 tabelas, notas, pedidos, pk por tenant, IdTenant, SqlBulkCopy, KeepIdentity, concurrency token, painel de importacao]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# BTech — importação das 77 tabelas, PK por tenant e detalhe no painel

## Resumo
Pedido: importar todas as tabelas e ver notas e pedidos no web. O bloqueio não era o importador —
era a modelagem multi-tenant, que nunca tinha sido terminada.

## Decisões do Jonas
Perguntado sobre as 50 tabelas de chave natural e o escopo, escolheu **corrigir a PK para
(IdTenant + chave)** e **importar tudo que é dado de negócio**.

## O que foi feito

### Diagnóstico
- As telas de `notas-fiscais`, `pedidos`, `financeiro`, `estoque` e 12 relatórios **já existiam**
  no [[BTech.Web]] consumindo a API — faltava o dado chegar, não a tela.
- A migração `004` só criou a **coluna** `IdTenant`; as PKs continuaram as do legado. 26 tabelas de
  negócio com chave natural colidiriam entre clientes.
- Bug já existente: `Empresas.CodEmpresa` é PK sem IDENTITY e o importador zerava a chave — toda
  empresa entrava com código 0 e a segunda do cliente se perdia.
- O schema Delphi **não declara FOREIGN KEY nenhuma** (`SELECT COUNT(*) FROM sys.foreign_keys` = 3,
  todas do sistema novo). Foi o que tornou a migração de PK viável.

### Entregue
[[pk-por-tenant-para-preservar-numero-do-legado]] — migração `020` (127 tabelas convertidas,
idempotente) e motor de cópia com `SqlBulkCopy` + `KeepIdentity`, preservando os IDs do legado.
`MapaImportacao` com as 77 tabelas em 9 módulos.

### A regressão que os testes pegaram
[[query-filter-nao-protege-update-entre-tenants]] — com a PK do banco mudada e a do modelo EF
igual, alterar o pedido nº 1 de um tenant alterava o do outro, em silêncio. Corrigido com
`IsConcurrencyToken` no `IdTenant`. **Sem o teste de dois tenants, isso teria ido para produção.**

### Web
Histórico de execuções expansível com uma linha por tabela; o que ficou de fora aparece em seção
própria. Corrigido `MODULOS_ORDEM`, que era lista fixa de 4 módulos e esconderia os 9 novos — o
próprio comentário no arquivo avisava do acoplamento.

## Validação
826 testes, 0 falhas, com 13 novos de integração contra SQL Server real (Docker local, via
`BTECH_TEST_SQLSERVER`).

**O front não foi verificado:** `tsc` e `eslint` travam por [[icloud-evicta-node-modules-e-tsc-trava]]
— 40.816 arquivos evictados. Revisão manual apenas; o CI decide.

## PRs
- API: https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/39 (empilhado sobre o #38)
- Web: https://github.com/B-Tech-Sistemas/BTech.Web/pull/21

Fila do dia: API #37 (.env), #38 (NFS-e), #39 (importação) e Web #21 — nessa ordem.

**Deploy:** parar a aplicação, rodar a `020`, e subir **já com o código novo** — a `020` com o
código anterior reabre a corrupção no UPDATE.

## Depois do merge
Os quatro PRs foram mergeados (API #37, #38, #39; Web #21). O #39 conflitou porque o #38 entrou por
squash — a branch tinha os commits originais e a main, o squash. Resolvido recriando a branch a
partir de `origin/main` e fazendo cherry-pick do commit: **aplicou sem conflito de conteúdo**, era
divergência de histórico. Ver [[branch-mergeada-por-squash-e-apagada-no-remoto]].

Relendo o `CLAUDE.md` depois do merge, achei uma ponta solta: `ProximoCodigoAsync` contava o máximo
global. Virou o PR **#40** — detalhe em [[query-filter-nao-protege-update-entre-tenants]].

## Aprendizados
- [[pk-por-tenant-para-preservar-numero-do-legado]]
- [[query-filter-nao-protege-update-entre-tenants]]

## Relacionado
- [[BTech.NFe.Api]] · [[BTech.Web]] · [[base-legada-btech-delphi]]
