---
tipo: projeto
titulo: BTech.Web
projeto: [BTech.Web, btech-nfe-web]
stack: [next, react, typescript, tailwind, playwright]
tags: [tipo/projeto, stack/next, empresa/btech, tipo/frontend]
palavras-chave: [btech, web, frontend, next, nextjs, react, tailwind, shadcn, playwright, dark mode, nfe]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-28
confianca: alta
---

# BTech.Web
## Resumo
Front-end do sistema NFe da BTech em Next.js 16 + React 19, consumindo a [[BTech.NFe.Api]].

## Contexto
`~/Documents/Repos/BTech/BTech.Web` · `github.com/B-Tech-Sistemas/BTech.Web` · branch `main`.
O `package.json` ainda se chama `btech-nfe-web` — e a continuacao do repositorio
[[btech-nfe-web]] em `Repos/Sites/`, que guarda o historico detalhado de como as telas nasceram.

## Detalhe

### Stack
| Peca | Versao |
| --- | --- |
| Next.js | 16.2.9 |
| React | 19.2.4 |
| Tailwind | 4 (via `@tailwindcss/postcss`) |
| Componentes | shadcn 4 + `@base-ui/react` |
| Icones | lucide-react |
| Graficos | recharts |
| Tema | next-themes (dark mode ponta a ponta) |
| Toast | sonner |
| E2E | Playwright |

### Comandos
```bash
npm run dev          # next dev
npm run build        # next build
npm run lint         # eslint
npm run test:e2e     # playwright test
npm run test:e2e:ui  # playwright test --ui
```

### Estrutura
```text
src/app/         rotas (admin, dashboard, login)
src/components/  ui, forms, dashboard
src/contexts/    contextos React
src/hooks/       hooks customizados
src/lib/         api.ts e utilitarios
tests/e2e/       specs Playwright
playwright/.auth  estado de sessao autenticada
```

### Atencao
`AGENTS.md` do repo avisa: **esta versao do Next tem breaking changes** em relacao ao
conhecimento de treino dos modelos. Consultar `node_modules/next/dist/docs/` antes de escrever
codigo. Ver [[next-16-nao-e-o-next-que-voce-conhece]].

### Rodar local
`scripts/dev-local.ps1` (Windows) / `scripts/dev-local.sh` (macOS): confere Node 20.9+, cria
`.env.local` do `.env.example` (`NEXT_PUBLIC_API_URL=http://localhost:5204`), testa `/health` da API,
reinstala deps quando precisa e roda `npm run dev`. `-ComApi` / `--com-api` sobe a API antes
(`../BTech.NFe.Api/scripts/setup-local.*`). Login local: `Administrador` / `123456`.
"Failed to fetch": ver [[failed-to-fetch-no-front-btech]].

### CI (workflow `seguranca.yml`: npm ci → npm audit --audit-level=high → npm run lint)
- Nunca tinha passado desde o #1; **verde desde o PR #11 (2026-09-11, `aaa3b4c`)**.
- TypeScript fixo em `^5.9.3` e ESLint em `^9`: o `typescript-eslint` do `eslint-config-next`
  aceita `typescript < 6.1` e o `eslint-plugin-react` dele usa `context.getFilename` (removido no
  ESLint 10). O `dependabot.yml` ignora `typescript >= 6.1.0` e `eslint >= 10.0.0` — revisar quando
  os plugins suportarem.
- `react-hooks/set-state-in-effect`: foi rebaixada para warn no #11 e **voltou a error** na branch
  `refactor/carregamento-sem-setstate-em-efeito` (47 ocorrencias migradas). Hooks novos:
  `useCarregar`, `useNoCliente`, `useValorLocal` (+ `lib/armazenamentoLocal`). Sessao/`AuthContext`
  le o localStorage por `useSyncExternalStore`. Ver [[carregar-dados-sem-setstate-em-efeito]].
- A emissao em lote (`BatchEmissionDialog`) e **simulada** (setTimeout + Math.random) — nao chama a API.
- `next dev` 16.3 reescreve o `AGENTS.md` sozinho (bloco `nextjs-agent-rules`) — commitar junto.

### Ambiente desta maquina
O `node_modules` fica evictado pelo iCloud (repo em `~/Documents`) e `tsc`/`eslint` travam —
ver [[icloud-evicta-node-modules-e-tsc-trava]] antes de concluir que o comando "esta lento".

### Tela de importacao (`src/app/admin/importacao`)
Desde 2026-09-11 usa `OrigemImportacao` (`{ databaseName }` ou `{ connectionStringLegado }`);
o navegador nunca recebe connection string da API. Ver [[importador-origem-por-databasename-e-allowlist]].

### Gap vs [[BTechPLUS.FrontEnd]] — 5 cadastros ausentes ou incompletos (varredura 2026-09-16)
O Jonas quer Api+Web 100% integrados e esta avaliando portar telas prontas do FrontEnd pausado.
`src/lib/api.ts` tem comentarios "NOTA:" proprios documentando o que existe/nao existe no backend —
usados para cruzar os achados abaixo. Nenhum link morto na sidebar (`AppSidebar.tsx`); toda ausencia
abaixo e falta real de tela, nao link quebrado.
- **Fornecedores** — pior caso: nem `api.fornecedores` existe no client. So aparece como `codFor`
  numerico cru (sem nome) em `/dashboard/financeiro` e `/dashboard/relatorios/contas-pagar`. Tambem
  nao existe UI de Compras/Entradas apesar do client ter `api.entradas` (CabEntrada/CorEntrada)
  completo e nao usado por nenhuma tela.
- **GruposProduto** — `api.gruposProduto` (CRUD completo) existe mas e codigo morto: zero telas
  usam, `ProdutoForm.tsx` nao tem campo de grupo, `relatorios/tabela-preco` aceita `idGrupo` na API
  mas a pagina nunca passa esse filtro.
- **TiposPagto** — ausente por completo, nem no client. So existe uma lista hardcoded de formas de
  pagamento (Dinheiro/Cartao/PIX/Outros) dentro de `/dashboard/nfce/nova`, sem relacao com um
  cadastro TiposPagto de verdade.
- **Vendedores** — `api.vendedores` CRUD completo e **funcional** como select em Pedidos e no
  relatorio de comissao, mas nao tem tela de cadastro propria (nao da pra criar/editar vendedor).
- **CondPagto** — `api.condicoesPagamento` CRUD completo, funcional como select em Pedidos, mas sem
  tela de cadastro propria; e **quebrado em Notas Fiscais**: o model tem `idcondPagto` e ele vai no
  payload de criacao, mas nao existe campo de UI pra preencher (nem na pagina real `nova/page.tsx`
  nem no form morto `NotaFiscalForm.tsx`) — toda nota sai com `idcondPagto: null`.

Tambem notavel: Produtos aqui **nao** recalcula preco de venda ao vivo a partir de
custo+margem (campos independentes); o [[BTechPLUS.FrontEnd]] faz esse calculo em tempo real e tem
tambem "Fechar Pedido" com baixa de estoque e workflow de status (PENDENTE/VALIDA/BAIXADO/
FATURADO/CANCELADO) que o Web nao tem — Pedidos aqui so tem Status Aberto/Fechado/Cancelado
livremente editavel, sem gerar movimento de estoque.

### Telas que PARECEM prontas mas nao estao realmente integradas ao backend real (2026-09-16, **atualizado apos Fase 6**)
Isso e uma resposta direta a exigencia "Api e Web devem estar 100% integrados". Historico: a
varredura da manha marcou `/certificado`, `/configuracao-tabela` e `/permissoes` como 404 quando
na verdade os dois primeiros ja existiam (so o comentario `// NOTA:` em `api.ts` estava
desatualizado) e o terceiro nem e usado por nenhuma tela (catalogo de permissoes e estatico em
`src/lib/permissions.ts`). Na Fase 6 (mesmo dia, a tarde) os mocks fiscais de verdade foram
resolvidos:
- ~~`BatchEmissionDialog`~~ — **corrigido**: chama `api.notasFiscais.emitir(id)` de verdade, em
  loop, um POST por nota selecionada (a Focus nao tem endpoint de lote — o loop e client-side).
- ~~`BatchCancelDialog`~~ — **corrigido**: `Promise.allSettled` chamando `api.notasFiscais.cancelar`
  por nota, reporta quantas foram e quantas nao foram.
- ~~Rotas antigas de notas fiscais (`itens/xml/danfe/consultar-status/cancelar/enviar-email/
  lote/exportar-xml`)~~ — **corrigido**: todas reais agora em `CabNotaController`, ver
  [[BTech.NFe.Api]] (`NotaFiscalEmissaoService`). O frontend nao precisou mudar rota nenhuma —
  as rotas que ja estavam escritas em `api.ts` (herdadas do FrontEnd) eram as certas, so faltava
  o backend implementar.
- ~~`/dashboard/perfis` permissoesDisponiveis~~ — removido de `api.ts` (dead code, ver acima).

**Corrigido na Fase 8 (2026-09-16, fim de tarde)** — Excel export nos 8 cadastros, tela de
Auditoria (`/dashboard/auditoria`, só super usuário, click-to-filter), impressão de Pedido — os
três eram gaps antigos (desde a Fase 3) fechados com padrões adaptados de [[BtechPlus.Gabriel]].
PRs [B-Tech-Sistemas/BTech.NFe.Api#36](https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/36) +
[B-Tech-Sistemas/BTech.Web#19](https://github.com/B-Tech-Sistemas/BTech.Web/pull/19).

**Corrigido na Fase 7 (2026-09-16, tarde)**:
- ~~`NotificationCenter`~~ — real agora. `GET /api/notificacoes` computa alertas de verdade
  (certificado a vencer/vencido, nota rejeitada pela SEFAZ, contingencia) — nao ha tabela de
  notificacoes, elas sao recalculadas a cada chamada; so o "ja vi essa" persiste
  (`NotificacaoLida`, por usuario). Marcar como lida e otimista na UI (aplica na hora, o POST vai
  em segundo plano) e sincroniza pra sempre no proximo refresh (poll a cada 2 min).
- ~~`/dashboard/page.tsx` sparkline/`MOCK_CHANGE`~~ — real agora. `GET /api/dashboard/kpis`
  devolve serie mensal de 7 meses (Cliente/Produto/Empresa por `DataCadastro`, CabNota modelo 55
  por `DataEmissao`) + variacao % mes atual vs anterior, tudo contado no banco (`CountAsync` novo
  no `IRepository<T>`, nao carrega a entidade inteira). `hasCertificado` tambem passou a vir daqui
  (`temCertificadoValido`).

**Ainda mock, fora do escopo desta rodada** (nao sao fiscais nem foram pedidos):
- `/admin/configuracoes` — so `localStorage`; **isso pode ser correto por design** (e a URL da API
  que o navegador vai chamar, nao dá pra buscar essa config na própria API antes de saber pra
  onde apontar) — avaliar se vale a pena antes de "corrigir".
- `NotaFiscalForm.tsx` — codigo morto, nao importado em lugar nenhum.
- "Atividade Recente" e "Notas Pendentes" no dashboard continuam como estado vazio estatico (nao
  sao dado fake, so nunca foram ligados a nada — diferente de mock, e so nao-implementado ainda).

### Padrao "criar sem sair do contexto" (2026-09-16)
`SeletorProduto`/`SeletorCliente` (busca + seleciona + "+ Cadastrar novo" inline) e
`NovoRegistroRapidoDialog` (generico, 1 campo) — usados em Pedidos, Notas Fiscais e Fornecedores.
Qualquer tela nova que precise de um select de Produto/Cliente/Vendedor/CondPagto/TiposPagto deve
reusar esses componentes em vez de um `<Select>` populado por lista inteira. Ver
[[2026-09-16-btechplus-frontend-comparacao-telas]] (Fase 4).

### Trava de status do Pedido (ABERTO/FECHADO/CANCELADO) — 2026-09-16
So pedido `ABERTO` pode ser alterado/excluido — vale pro backend (`CabPedidoController`, 400 se
nao for) e pro frontend (botao/tela desabilitados). **Atencao**: esse vocabulario (ABERTO/FECHADO/
CANCELADO) e do Web — nao confundir com PENDENTE/BAIXADO/FATURADO/VALIDA/CANCELADO do
[[BTechPLUS.FrontEnd]], que e outro sistema com outro modelo de status.

### PRs abertos desta varredura (2026-09-16)
Backend: [B-Tech-Sistemas/BTech.NFe.Api#35](https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/35)
(`feature/condpagto-camelcase-e-trava-pedido-aberto`). Frontend:
[B-Tech-Sistemas/BTech.Web#18](https://github.com/B-Tech-Sistemas/BTech.Web/pull/18)
(`feature/cadastros-fornecedores-vendedores-e-criar-sem-sair-do-contexto`) — **depende do PR do Api**
(camelCase de `CabPedido`/`Fornecedore` e a trava de status `ABERTO`), mergear os dois juntos.

### 5 cadastros portados do BTechPLUS.FrontEnd (2026-09-16)
Fornecedores, Vendedores, Grupos de Produto, Condicoes de Pagamento e Formas de Pagamento ganharam
tela propria (`/dashboard/fornecedores|vendedores|grupos-produto|condicoes-pagamento|tipos-pagamento`,
cada um com lista+novo+editar). Detalhe completo, incluindo 2 bugs de backend corrigidos nessa
mesma leva (CondPagto sem `ProximoCodigoAsync` e casing de `CabPedido.Idempresa/Idcliente/Idpagto/
Idvendedor`), em [[2026-09-16-btechplus-frontend-comparacao-telas]] e
[[camelcase-id-fields-sem-fronteira-de-palavra]]. Ainda faltam (nao mexido nesta rodada): a lista de
telas "parecem prontas mas nao integradas" logo acima (Perfis, Certificado Digital, lotes de NFe,
notificacoes, dashboard mock).


### Emissão e Focus (2026-09-26)
"Emitir Nota" salva e transmite; Transmitir por linha; consulta de status por `refFocus`;
polling da SEFAZ em `lib/emissaoNota.ts`; cartão `IntegracaoFocusCard` na edição da empresa;
tela de certificado removida; sidebar com um grupo aberto e flyout `position: fixed`.
Verificar front: copiar para o scratchpad e rodar `npm ci`/`tsc`/`eslint`/`next build` lá.
Sessão: [[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]].


### Conferir e corrigir a nota (2026-09-28)
`components/notas/EditorNotaFiscal.tsx` serve a nova nota e `/dashboard/notas-fiscais/[id]/editar`;
`lib/fiscal.ts` tem origem/CSOSN/CST. Cadastros de Natureza de Operação e NCM. PR #26.

## Relacionado
- [[BTech.NFe.Api]]
- [[btech-nfe-web]]
- [[next-16-nao-e-o-next-que-voce-conhece]]

## Atualização 2026-10-01 — tela única de nota
- Notas (produto, serviço, devolução, importação) abrem em `/dashboard/notas-fiscais/nova` (tipo e nota na query; helpers em `src/lib/rotasNota.ts`). Rotas `/editar` e `/nfse/*` só redirecionam. Menu: um item "Notas Fiscais"; serviço é aba da lista.
- Trabalhar sempre em worktree de `origin/main` (`_wt/nota-unica-web`), com `npm ci` próprio. Ver [[2026-10-01-btech-nota-unica-produto-servico-devolucao-importacao]] e [[checkout-local-atras-da-main-e-ref-duplicada-do-icloud]].
