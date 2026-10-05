---
tipo: sessao
titulo: "BTech: testes de contrato front×API e verificação do front fora do iCloud"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next, vitest, openapi]
tags: [tipo/sessao, stack/next, stack/dotnet]
palavras-chave: [contrato, openapi, snapshot, vitest, tsc, next build, icloud, ~/Code, CONTRATO_API_TOKEN, DIVERGENCIAS_CONHECIDAS]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# BTech: testes de contrato front×API e verificação do front fora do iCloud

## Resumo
API#57 (snapshot `docs/openapi.json` + teste) e Web#39 (vitest `tests/contract`, workflow `verificacao.yml`); front verificado de verdade num clone em `~/Code/BTech.Web`.

## Contexto
Pedido do Jonas: "testes no front para saber se o backend mudar o contrato não vai quebrar", "teste o front em outra pasta", "crie os testes no SQL". O `node_modules` do checkout em `~/Documents` está evictado pelo iCloud (ver [[icloud-evicta-node-modules-e-tsc-trava]]), então nada do front tinha passado por tsc/build.

## Detalhe
- **Verificação do front fora do iCloud**: `git clone` em `~/Code/BTech.Web` + `npm ci`. Lá `tsc --noEmit` leva ~4 s, `next build` ~12 s. A `main` passou: tsc limpo, lint 0 erros (26 avisos antigos), build ok. Agentes criam worktree a partir desse clone e ligam `node_modules` por symlink (`ln -s ~/Code/BTech.Web/node_modules`).
- **Contrato**: a API gera o OpenAPI (Swagger só fora de Produção) e `OpenApiSnapshotTests` compara com `docs/openapi.json` (chaves ordenadas; `ATUALIZAR_CONTRATO=1` regenera). O Web guarda cópia em `contract/openapi.json` e o vitest confere (1) toda chamada de `src/lib/api.ts` (método+rota, extraída pelo AST do TypeScript) existe no contrato e (2) campos das interfaces usadas em `request<X>` existem na resposta, com o mesmo tipo. Schemas do Swashbuckle vêm com namespace completo (`BTech.NFe.Domain.Models.X`): casar por sufixo `.X`.
- **Achados reais**: tela de certificado (#37) mergeada antes do PR #55 da API — front lê `titular/cnpj/inicioValidade/avisos` que a main da API não devolve; `LoginResponse.permissoes` nunca chega e nenhuma tela chama `can()` (permissões por chave de perfil não são aplicadas, só papéis); tipos mortos `Empresa/Cliente/Produto` em types.ts (removidos).
- **Limite**: só verifica o que o contrato declara; retorno `ActionResult` não tipado no back não é checado. Tipar retornos (DTOs) aumenta a cobertura.
- **CI**: `verificacao.yml` (tsc + contrato + build por PR; diário contra a main da API se houver segredo `CONTRATO_API_TOKEN`).
- Armadilhas da sessão: `pgrep -f` dentro de um `until` do Monitor casa o próprio comando (loop infinito); o scratchpad da sessão é apagado na virada do dia — não guardar trabalho ali; PRs mergeados fora de ordem (Web#37 antes da API#55) quebram silenciosamente o contrato.

## Fechamento do dia (integração)
- PRs abertos: API #57 (contrato), #58 (inutilização/CC-e/cancelamento), #59 (DTOs + máscara CPF), #60 (NFC-e/DIFAL), #61 (testes SQL Server); Web #38–#42. Mergeados no dia: #53–#56.
- **Integração**: `integ/api-todos` e `integ/web-todos` (origin) unem tudo com conflitos resolvidos (Web: coluna de ações do nfce/page.tsx; API: CancelarAsync em NotaFiscalEmissaoService). API: Unit 841, Contract 242, Functional 70, SqlServer 10/10 (SQL Server 2022 real via Testcontainers, amd64 emulado). Web: tsc, lint, build e contrato ok.
- **Achados**: `GET /api/usuarios` devolvia o hash BCrypt da senha (PR #59 corrige); UPDATE com `IdTenant` alheio movia a linha de outro tenant porque só o valor corrente era forçado, não o `OriginalValue` (PR #61); testes de contrato pegaram front lendo rotas/campos que a API ainda não tinha.
- **Operacional**: `git clone` fora do iCloud (`~/Code`) resolve tsc/build/git travando; `npm ci` em worktree (symlink de node_modules quebra o Turbopack). Agentes em paralelo: commitar/dar push no primeiro bloco (limite de uso derrubou 4 de 6); `git` em worktrees dentro de `~/Documents` trava — preferir clone em `~/Code`.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[icloud-evicta-node-modules-e-tsc-trava]]
- [[openapi-gerado-do-codigo]]

## Pós-merge (mesmo dia)
- Limpeza: worktrees extras de ~/Code removidos, 13 duplicatas `* 2.*` do iCloud apagadas (nenhuma versionada). Armadilha: `git checkout` de branch já usada em outro worktree falha e o `reset --hard` seguinte cai na `main` do clone — restaurar pelo reflog; sempre operar no worktree que já tem a branch.
- API#62: DIFAL não cobrado de optante do Simples (RC28968/2023, RC32359/2025 SEFAZ-SP), aviso de NFC-e interestadual (NFC-e = presencial/entrega na UF; Focus aceita presença 1 e 4). Pendente com contador: ST/sublimite, DI/DUIMP, IBS/CBS.
- API#63/Web#43: DTOs de Fornecedor e Vendedor, CPF mascarado na lista, `documento-existe` no servidor. Sem migrar: Produto, pedidos/notas, ContasCors, TiposPagto, tabelas de apoio, `GET /api/importador`.
