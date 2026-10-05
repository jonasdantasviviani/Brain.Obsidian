---
tipo: sessao
titulo: Varredura comparativa BTechPLUS.FrontEnd vs BTech.Web (em andamento)
projeto: [BTechPLUS.FrontEnd, BTech.Web]
stack: [next, react, typescript]
tags: [tipo/sessao, empresa/btech, tipo/frontend]
palavras-chave: [comparacao telas, gap analysis, frontend pausado, cadastros, fornecedores, vendedores, condpagto, tipospagto, grupos produto]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: media
---

# Varredura comparativa BTechPLUS.FrontEnd vs BTech.Web (em andamento)
## Resumo
Sessao aberta para mapear telas/funcionalidades do [[BTechPLUS.FrontEnd]] (pausado) que existem
prontas la mas estao ausentes ou mais fracas no [[BTech.Web]] (oficial).

## Contexto
Jonas adicionou os tres repos (API, Web, Front End) ao mesmo workspace e pediu: API e Web sao os
oficiais e devem estar 100% integrados; o Front End foi um inicio de projeto pausado que tem
cadastros bons prontos, e ele quer trazer o que vale a pena para o Web.

## Detalhe

### O que foi feito nesta sessao
1. Li as notas existentes ([[btech-nfe-web]], [[BTech.Web]]) — nenhuma cobria o BTechPLUS.FrontEnd.
2. Listei a arvore `app/` dos dois repos por `find`. BTechPLUS.FrontEnd tem rotas dedicadas para
   `condpagto`, `fornecedores`, `grupos`, `tipospagto`, `vendedores` e `auditoria` que nao aparecem
   como diretorio de primeiro nivel em `src/app/dashboard` do BTech.Web (precisa confirmar se estao
   embutidas em outras telas antes de declarar gap real).
3. `git log` do BTechPLUS.FrontEnd tem um unico commit "Start" — origem v0.dev
   (`package.json` name `my-v0-project`), sem historico util. `package.json` revela libs extras que
   o BTech.Web nao tem: `jspdf`/`jspdf-autotable` (export PDF), `xlsx` (export Excel),
   `@tanstack/react-table`. Tambem aparecem `mssql`/`typeorm`/`@nestjs/typeorm` como dependencia —
   **suspeito, precisa verificar se e usado de verdade** (acesso direto a banco pelo frontend seria
   falha grave; mais provavel resquicio do template v0).
4. Criei a nota de projeto [[BTechPLUS.FrontEnd]] com esse levantamento estrutural.
5. Disparei 2 agentes Explore em paralelo (um por repo) para inventario fino: por rota, campos de
   formulario, validacoes (CPF/CNPJ/CEP etc.), logica de negocio, e — critico — se cada tela esta
   realmente ligada a API real ou e mock/simulada (sabemos por [[BTech.Web]] que `BatchEmissionDialog`
   e simulado com `setTimeout`). Pedi tambem checagem explicita no BTech.Web por Fornecedores,
   Vendedores, GruposProduto, CondPagto, TiposPagto (podem estar como select dentro de outro form,
   nao como tela propria).

### Resultado final
Os dois agentes Explore devolveram. Detalhe completo por modulo esta em [[BTechPLUS.FrontEnd]]
(inventario do FrontEnd) e [[BTech.Web]] (secao "Gap vs BTechPLUS.FrontEnd" + "Telas que parecem
prontas mas nao estao integradas"). Resumo do gap real (FrontEnd tem, Web nao tem tela de cadastro):

| Entidade | No FrontEnd | No Web |
|---|---|---|
| Fornecedores | Cadastro completo, dobra como Transportadora | Nada — nem client de API existe |
| GruposProduto | Cadastro completo, usado em Produtos | Client de API existe mas morto, zero telas usam |
| TiposPagto | Cadastro completo + tabela SEFAZ de referencia clicavel | Nada — so lista hardcoded no NFC-e |
| Vendedores | Cadastro completo | So funciona como select em Pedidos/comissao, sem tela propria |
| CondPagto | Cadastro completo + construtor de 12 parcelas | So select em Pedidos, sem tela; quebrado (campo ausente) em Notas Fiscais |

Prioridade sugerida ao Jonas: **Fornecedores primeiro** (gap total, e usado em Financeiro/Compras),
depois **Vendedores + CondPagto** (so falta a tela de cadastro, o client de API ja existe no Web —
portar e mais rapido), depois **TiposPagto** (cadastro pequeno + a tabela SEFAZ e um bom achado
pronto pra reusar), **GruposProduto** por ultimo (baixo impacto, ninguem usa hoje).

Achado paralelo, fora do escopo original mas relevante pro pedido de "100% integrado": varias telas
do Web parecem prontas mas chamam endpoint que nao existe no backend real hoje (Perfis, Certificado
Digital, emissao/cancelamento em lote de notas, config do painel admin, sync de config de coluna) —
lista completa em [[BTech.Web]]. Isso e uma pendencia de integracao Api<->Web independente da
questao do FrontEnd.

**Nota de arquitetura**: o FrontEnd pausado falava com uma API Node/NestJS separada (nao a
`BTech.NFe.Api` .NET), entao portar uma tela significa reescrever a chamada de API para o client
`src/lib/api.ts` do Web (ou criar o endpoint no back se nao existir), nao copiar o codigo de
fetch 1:1.

### Fase 2 — implementacao (2026-09-16, mesma sessao, apos "pode fazer tudo")
Jonas autorizou portar os 5 cadastros para o [[BTech.Web]]. Descoberta-chave antes de escrever
codigo: os 5 controllers (`FornecedoreController`, `VendedoreController`, `CondPagtoController`,
`TiposPagtoController`, `GruposProdutoController`) **ja existem** na API (CLAUDE.md ja listava os
services registrados, so faltava o cliente TS + a tela). Ou seja, essa fase foi **so frontend**,
sem precisar inventar contrato novo — exceto 2 achados que exigiram tocar o backend:

1. **`CondPagto.CodPagamento` nao e IDENTITY** (como `Empresas.CodEmpresa`/`CabPedidos.NumeroPedido`
   ja documentados no CLAUDE.md) e `CondPagtoService` nao tinha o `ProximoCodigoAsync` — corrigido
   igual ao `EmpresaService` (`src/Application/.../Services/CondPagtoService.cs`), com teste novo
   `CreateAsync_SemCodigo_AtribuiProximoCodigo`.
2. **Bug de casing confirmado empiricamente** (rodei um console .NET so pra testar
   `JsonNamingPolicy.CamelCase.ConvertName`): campo scaffoldado do legado sem maiuscula interna
   (`Idempresa`, `Idcliente`, `Idpagto`, `Idvendedor` — "Id" + palavra toda minuscula) serializa
   igual (`idempresa`, nao `idEmpresa`), enquanto o resto do app sempre espera camelCase de verdade
   (`idEmpresa`). Isso **quebrava de verdade** `CabPedido` no Web: filtro de Cliente/Vendedor/
   Condicao na listagem de Pedidos nunca batia (lista sempre vazia com filtro ativo), e a tela de
   editar pedido lia `undefined` pros 4 campos — salvando sem reselecionar apagava Empresa/Cliente/
   Vendedor/Condicao do pedido silenciosamente. Corrigido **na fonte**: `[JsonPropertyName]` em
   `CabPedido.Idempresa/Idcliente/Idpagto/Idvendedor` (`src/Domain/.../Entities/CabPedido.cs`) e em
   `Fornecedore.Idvendedor` (mesmo entity que eu ia expor numa tela nova) — mantem o frontend como
   estava (`idEmpresa` etc.), so a serializacao do backend passou a bater. **Nao mexi** em
   `CabNota.Idempresa/Idcliente` (mesmo padrao quebrado) porque o frontend de Notas Fiscais ja usava
   `idempresa`/`idcliente` minusculo por acidente-que-deu-certo — mexer ali trocaria um "funciona"
   por um "quebra". Ver [[BTech.NFe.Api]] e [[BTech.Web]].
3. Campo `idcondPagto` (esse ja converte certo, tem "Pagto" maiusculo) estava **de verdade ausente**
   na tela de Nova Nota Fiscal (`src/app/dashboard/notas-fiscais/nova/page.tsx`) — toda nota saia com
   condicao de pagamento nula. Adicionado select populado por `api.condicoesPagamento.list`.

Entregue: 5 modulos completos no Web (`/dashboard/fornecedores`, `/vendedores`,
`/grupos-produto`, `/condicoes-pagamento`, `/tipos-pagamento` — cada um com lista+novo+editar,
seguindo o padrao existente de Clientes/Produtos: form inline por pagina, nao o `ClienteForm.tsx`
morto), entradas novas em `src/lib/api.ts` (`fornecedores`, `tiposPagto`; `gruposProduto`/
`vendedores`/`condicoesPagamento` ja existiam), item novo no `AppSidebar.tsx` (grupo Cadastro).
Fornecedores usa os 3 cadastros novos como select (Vendedor, Condicao de Pagamento, Forma de
Pagamento) — igual ao padrao ja usado em Pedidos. Formas de Pagamento leva a tabela oficial SEFAZ de
meio-de-pagamento/bandeira (`src/lib/sefazPagamento.ts`), a peca que o FrontEnd pausado tinha pronta
e valia a pena trazer 1:1. Nao existe `Checkbox` no design system do Web (so os componentes shadcn
que ja estavam instalados) — troquei o checkbox do FrontEnd por Select Sim/Nao, consistente com o
resto do app.

**Ainda pendente desta sessao**: verificacao de lint/typecheck do Web travou por causa de
[[icloud-evicta-node-modules-e-tsc-trava]] (mesma armadilha ja catalogada) — `dotnet build` +
testes unitarios/contract do backend passaram 100% (29+24 testes). Nao toquei na Fase 3 (telas do
Web que chamam endpoint inexistente: Perfis, Certificado Digital, emissao/cancelamento em lote,
notificacoes, dashboard mock) — ver a lista completa em [[BTech.Web]]; e um corpo de trabalho
separado e maior (varias precisam de controller novo no backend), melhor tratado como proxima rodada
com o Jonas do que emendado sem parar pra confirmar prioridade.

### Fase 3 — varredura de UX/fluxo de trabalho (2026-09-16, mesmo dia)
Jonas pediu uma segunda passada, mais fina: nao "a tela existe" mas "o que a tela do FrontEnd faz de
jeito mais esperto que a do Web" — citou como exemplo: ao escolher produto num pedido, se nao existe
ainda, ja consegue cadastrar ali mesmo. Rodei 2 agentes Explore em paralelo lendo **linha a linha**
(nao resumo) os 8 pares Form+Table de cadastro + Pedidos/NotasFiscais/Auditoria/sidebar/login dos
dois repos.

**Resposta direta ao exemplo do Jonas — nao existe em nenhum dos dois repos.** Verificado de forma
independente por 2 agentes: o FrontEnd so tem um modal generico de busca-e-selecionar
(`GenericSearchModal` em `PedidosEditModal.tsx`, e outro embutido em `ProdutosForm.tsx` para
Grupo/Classificacao Fiscal) — sem botao "+ Novo"/"Cadastrar" em lugar nenhum. Pior: no
`ProdutosForm.tsx` a busca de Fornecedor/Marca **nem chega a ser chamada** na tela (so Grupo e
Classificacao Fiscal tem botao de verdade) — codigo morto/inacabado. Ou seja, o Jonas lembrou de algo
que pretendia construir ou viu em outro lugar, nao algo que existe no codigo — vale saber que **e uma
ideia nova**, nao um resgate.

**Achado mais valioso de verdade** (perda real, nao cosmetica): `ProdutosForm.tsx:206-246` no
FrontEnd calculava **PrecoVenda = Custo + Custo×Margem/100 ao vivo**, a cada tecla (com par
independente pra Revenda) quando a caixa "Usar Margem" esta marcada. No Web (`produtos/novo` e
`[id]/editar`), Custo/Margem/Preco sao 3 campos totalmente manuais e independentes — a conta que
existia sumiu.

**Segundo achado mais valioso**: checagem de CPF/CNPJ duplicado **antes de salvar** (Clientes,
Empresas, Vendedores via `GET .../check-documento/:doc`; Fornecedores varrendo a lista inteira
client-side) existe no FrontEnd e nao existe em nenhum lugar do Web hoje — Web so descobre duplicata
se a API/banco reclamar depois do submit.

**Gaps confirmados em Pedidos** (o Web aqui ficou mais fraco que o prototipo, nao so "sem feature
extra"):
- Sem trava de status: Web deixa **editar e excluir pedido em qualquer status** (inclusive
  FECHADO/FATURADO); FrontEnd trava edicao fora de PENDENTE e so libera Cancelar/Fechar conforme
  status — isso e regressao, nao so ausencia.
- Sem fluxo guiado de "Fechar Pedido" (o FrontEnd explica na tela que vai gerar saida de estoque e
  atualizar saldo antes de confirmar; Web so tem um `<Select>` de status livre).
- Sem impressao (A4 nem matricial 50 colunas).
- Seletor de produto no pedido do Web e um `<Select>` com a lista inteira sem busca — pior que o
  modal com filtro do FrontEnd (o Jonas sentiu isso mesmo sem lembrar certo o detalhe).
- Sem desconto em % (so R$, sem alternancia bidirecional).
- Sem confirmar qtde/preco antes de adicionar item a linha (Web adiciona linha vazia editavel
  direto).
- Sem cor de linha por status / duplo-clique pra abrir em modo consulta / export Excel / painel de
  itens embutido na lista.

**Gap estrutural maior**: tela de **Auditoria nao existe no Web** — o FrontEnd tem log com filtro
clicavel por Usuario/Tabela/Funcao, alimentado pelo header `x-usuario` que ja carimba toda chamada
(`ClientLayout.tsx`); no Web nao ha equivalente nenhum (nem tela nem, aparentemente, o mecanismo de
carimbar usuario na chamada).

**Padrao repetido nos 8 cadastros** (Clientes/Empresas/Fornecedores/Vendedores/Produtos/Grupos/
CondPagto/TiposPagto), baixo-mas-real impacto:
export Excel (xlsx+file-saver) ausente em 100% do Web; modo "Consultar" (visualizar sem editar) +
barra de acao por selecao (Incluir/Alterar/Consultar/Excluir) + duplo-clique ausentes (Web vai direto
lapis=editar/lixeira=excluir); modal de confirmacao de exclusao estilizado (mostra o nome do
registro) vira `confirm()` nativo no Web; redimensionar coluna arrastando ausente no Web.

**Onde o Web ja e melhor** (nao e so perda, vale registrar pra nao subestimar o Web): componentes
`CampoCep`/`CampoCnpjCpf` compartilhados com validacao ao vivo (`aria-invalid`) em vez de
copiar-colar por arquivo; `lib/sefazPagamento.ts` como `<Select>` de verdade em vez do modal de
ajuda-e-copiar-codigo do FrontEnd; painel de configuracao de colunas (mostrar/ocultar, arrastar
ordem, persistido) no `DataTable`, sem equivalente no FrontEnd; upload de foto do produto (base64,
guard de 2MB); `SeletorProduto.tsx` com busca assincrona debounced (300ms) usada em Notas Fiscais —
so nao tem opcao de criar, igual o resto.

Prioridade sugerida pro Jonas decidir o que atacar depois: (1) calculo de preco por margem em
Produtos, (2) checagem de documento duplicado antes de salvar, (3) trava de status + fechamento
guiado de Pedido (e regressao de seguranca de dados, nao so UX), (4) tela de Auditoria, (5) o resto
(Excel, modo consulta, impressao de pedido, resize de coluna) como polimento posterior.

### Fase 4 — implementacao ("arrume tudo" + criar sem sair do contexto), 2026-09-16
Jonas pediu pra corrigir os achados da Fase 3 e generalizar um padrao novo: poder cadastrar
Produto/Cliente/Vendedor/etc. **sem sair da tela onde esta usando** (o exemplo dele foi produto
dentro de um pedido). Entregue nesta rodada:

**1. Padrao "criar sem sair do contexto" (novo, nao existia em nenhum dos dois repos)**
- `components/forms/NovoProdutoRapidoDialog.tsx`, `NovoClienteRapidoDialog.tsx`,
  `NovoRegistroRapidoDialog.tsx` (generico, 1 campo — usado por Vendedor/CondPagto/TiposPagto).
- `SeletorProduto.tsx` ganhou um rodape "+ Cadastrar novo produto" que abre o dialog e ja
  seleciona o criado. `SeletorCliente.tsx` e novo, mesmo padrao (busca+seleciona+cria) mas
  filtrando client-side (nao existe endpoint de busca de cliente no backend, e o volume e
  pequeno — a tela ja carrega ate 200 de uma vez).
- Aplicado em: Pedidos (novo/editar — troquei o `<Select>` de Produto por `SeletorProduto` e o de
  Cliente por `SeletorCliente`, o que de quebra tambem resolveu o achado da Fase 3 "seletor de
  produto no pedido e lista cheia sem busca"), Notas Fiscais nova (Cliente), Fornecedores
  novo/editar (botao "+" ao lado de Vendedor/Condicao de Pagamento/Forma de Pagamento).

**2. Calculo de preco por margem em Produtos** — campo "Calcular preco pela margem" (checkbox
HTML puro, o design system nao tem `Checkbox` do shadcn instalado); quando ligado, Preco de Venda
fica somente-leitura e recalcula a cada tecla em Custo/Margem. Em `novo` vem ligado por padrao; em
`editar` vem desligado (nao quis recalcular por cima de um preco que ja existia sem o usuario pedir).

**3. Checagem de documento duplicado antes de salvar** — `lib/validacaoCadastro.ts` ganhou
`documentoDuplicado()`, que varre a lista ja carregada (sem endpoint novo no backend — mesma
logica que o FrontEnd usava pra Fornecedores, so que generalizada). Aplicado em Clientes, Empresas,
Vendedores (CPF) e Fornecedores, novo e editar (8 arquivos).

**4. Achado serio durante a Fase 4, corrigido**: a trava de status do Pedido do FrontEnd usava
"PENDENTE" — mas o Web **nao usa esse vocabulario**, usa ABERTO/FECHADO/CANCELADO (confirmado no
proprio `StatusBadge` e no payload de criacao). Se eu tivesse portado a trava como "PENDENTE" ao pe
da letra, **todo pedido criado pelo Web ficaria travado pra edicao assim que criado** — peguei isso
por code review antes de rodar, nao em producao. A trava correta ficou: so pedido com
`Status == "ABERTO"` pode ser alterado/excluido, em dois lugares:
- Backend: `CabPedidoController.Update/Delete` agora le o status atual **antes** de
  `AtualizarParcialAsync` (que muta a mesma instancia — ler depois pegaria o status novo, nao o
  antigo) e devolve 400 se nao for ABERTO. Precisou tornar `ServiceBase.UpdateAsync` `virtual`
  (so faltava esse, `Create/Get/Delete` ja eram). Teste novo em `CabPedidoContractTests`
  (`Update_PedidoNaoAberto_Retorna400`) cobre a transicao permitida (ABERTO->FECHADO) e a bloqueada
  depois. Essa trava nao existia — Web deixava editar/excluir pedido fechado, que era regressao
  real vs o FrontEnd.
- Frontend: botao Excluir na lista desabilita quando `status !== 'ABERTO'` (com tooltip), tela de
  editar mostra banner e desabilita os 3 botoes de Salvar quando bloqueado.

**Verificacao**: `dotnet build` limpo, 478 unit + 200 contract + 42 functional = 720 testes
passando no backend; `npx tsc --noEmit` e `npm run lint` limpos no Web (27 warnings pre-existentes,
0 novos, 0 erros) em duas rodadas (apos a Fase 4a e de novo apos a Fase 4b).

**Ainda nao feito desta lista** (Jonas nao pediu explicitamente nesta rodada, ficou como proximo
passo): export Excel nos cadastros, tela de Auditoria (Fase 3 apontou como o maior gap estrutural —
precisa de tabela append-only nova, ver [[26-trilha-de-auditoria]], regra de seguranca sem dono em
nenhum projeto), impressao de Pedido (A4 + matricial), desconto em % no Pedido, confirmar qtde/preco
antes de adicionar item, cor de linha por status + duplo-clique pra visualizar, modo "Consultar"
generico, redimensionar coluna.

## Fase 5 — commit e PR (2026-09-16)
Jonas pediu "commite tudo e abra PR". Sao dois repositorios separados, cada um com seu PR:
- **BTech.NFe.Api**: branch `feature/condpagto-camelcase-e-trava-pedido-aberto`, commit `537678f`,
  PR [#35](https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/35).
- **BTech.Web**: branch `feature/cadastros-fornecedores-vendedores-e-criar-sem-sair-do-contexto`,
  commit `d314ea3`, PR [#18](https://github.com/B-Tech-Sistemas/BTech.Web/pull/18) — descricao do PR
  linka o companheiro no Api e pede merge junto (o frontend depende do fix de camelCase e a trava de
  status so fica completa com os dois lados).

**Armadilha nova pega aqui**: os dois primeiros `git push` (e a checagem de `git log`/`remote -v`)
foram disparados em paralelo, um por repo, no mesmo Bash tool — como "working directory persiste
entre comandos mas shell state nao", dois `cd` concorrentes na mesma sessao de shell correram e o
segundo comando (`git push` do Web) executou com o `cwd` ainda no Api, tentando empurrar a branch do
Web pro remoto errado (falhou com "does not match any", sem dano — so nao empurrou). Corrigido
rodando os comandos de git entre repos **sempre sequenciais**, nunca em paralelo, mesmo que sejam
"independentes" a primeira vista. Ver [[cd-paralelo-entre-repos-no-mesmo-shell-corrompe-comando]].

## Fase 6 — mocks fiscais reais via Focus + rotas faltantes (2026-09-16, tarde)
Jonas pediu "Retire os mocks, e crie as rotas faltantes. Toda parte fiscal é com a Focus, eles
tem documentação bem detalhada" — resposta direta a lista de "telas que parecem prontas" (achado
anterior da mesma sessao). Primeiro passo, antes de qualquer codigo: reconferi 3 itens dessa lista
direto no codigo do backend (nao confiar so na memoria) e descobri que **2 estavam errados** —
`/api/empresas/{id}/certificado` e `/api/configuracao-tabela` ja existiam de verdade, so o
comentario `// NOTA:` em `api.ts` estava desatualizado. Corrigido na memoria antes de prosseguir
(ver [[BTech.Web]]).

**O que foi implementado de verdade**:
- Novo `NotaFiscalEmissaoService` (BTech.NFe.Api) — ponte entre CabNota/CorNota e a Focus NFe.
  Monta `EmitirNfeRequest` a partir de Empresa (emitente)/Cliente (destinatario)/CorNota (itens)/
  NaturezaOperaco (PIS/COFINS)/TiposPagto (forma de pagamento), usando os campos ja computados
  pelo sistema legado (nao inventa calculo de imposto do zero).
- `CabNotaController` ganhou `POST .../emitir`, `POST .../consultar-status`, `POST .../cancelar`,
  `POST .../enviar-email`, `GET .../xml`, `GET .../danfe`, `GET .../itens`,
  `GET lote/exportar-xml` (zip) — todas reais, chamando a Focus por baixo.
- `CabNotaService.AtualizarStatusFocusPorRefAsync` agora tambem traduz o status da Focus pro
  vocabulario legado (`CabNota.Status`) e grava `Idnfe`/`Idprotocolo` — antes so gravava os campos
  novos `StatusFocus`/`ChaveNfe`, a tela nunca refletia o status real.
- Frontend: `BatchEmissionDialog`/`BatchCancelDialog` trocaram o mock (`setTimeout`+
  `Math.random`) por chamadas reais em loop; `api.ts` limpo dos comentarios `// NOTA:`
  desatualizados; `notasFiscais.emitir()` novo.

**Achado mais serio da rodada** (pego so por checar a doc oficial da Focus antes de codar, exatamente
como o Jonas pediu): o modelo `EmitirNfeRequest`/`ItemNfeRequest` que ja existia no repositorio
usava nomes de campo **inventados** (`cst_icms`, `origem`, `ncm`, `ie_emitente`...) que nao
existem na API real da Focus (os nomes reais tem prefixo por tributo:
`icms_situacao_tributaria`, `icms_origem`, `codigo_ncm`, `inscricao_estadual_emitente`...) e
faltava o grupo de IPI inteiro. Se a emissao real tivesse sido tentada antes dessa checagem, a
Focus teria ignorado os campos de imposto (nome de chave nao reconhecido) ou rejeitado por campo
obrigatorio faltando (`local_destino`, `valor_produtos`, `valor_total` nem existiam no modelo).
Detalhe completo em [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]].

**Segundo achado**: NCM (obrigatorio por item na Focus) nao existia em lugar nenhum do schema — e
a tela de Produto tinha um campo rotulado "NCM" que na verdade estava ligado a `codFiscal` (outro
conceito) e **nem ia no payload de salvar**. Corrigido com migration + coluna nova
(`Produtos.Ncm`) e campo de verdade na tela. Ver
[[campo-ncm-do-produto-era-codfiscal-e-nem-salvava]].

**Verificacao**: backend — `dotnet build` limpo, 481 unit + 200 contract + 42 functional passando
(so a suite de integracao tem 1 falha pre-existente, sem SQL Server local, nao relacionada).
Frontend — `npx tsc --noEmit` e `npm run lint` limpos (27 warnings pre-existentes, 0 novos).

**Deliberadamente fora do escopo desta rodada** (nao sao fiscais): `NotificationCenter` (mock),
sparkline do dashboard (mock), `/admin/configuracoes` (so localStorage — pode ser correto por
design, ver [[BTech.Web]]). Tambem nao auditado: `EmitirNfceRequest` (NFC-e) pode ter o mesmo
problema de nomes de campo do NF-e, nao foi conferido. E: NF-e emitida por este caminho **precisa
de teste em homologacao da Focus antes de produção** — DIFAL e Imposto de Importacao nao sao
mapeados, e a logica de IPI/PIS/COFINS depende de dado que pode nao estar 100% preenchido pra
todo produto/natureza de operacao hoje.

## Fase 7 — NotificationCenter e sparkline reais (2026-09-16, fim de tarde)
Jonas: "arrume notification center e o sparkline, tire do mock. Crie rotas na API e faça
funcional no front." Resposta direta ao que ficou fora de escopo na Fase 6.

**Backend novo**: `IRepository<T>.CountAsync(predicate)` (SQL COUNT, sem materializar entidade) —
`DashboardService` usa 28x (4 entidades x 7 meses) pra montar a serie mensal de
`GET /api/dashboard/kpis`. `NotificacaoService`/`NotificacaoController` (`GET/POST
/api/notificacoes`) computam alertas reais (certificado a vencer/vencido, nota rejeitada,
contingencia) sem guardar evento nenhum — so a marca de leitura por usuario persiste (tabela nova
`NotificacoesLidas`, migration `018`). Ver [[BTech.NFe.Api]] pro detalhe completo.

**Frontend**: `NotificationCenter.tsx` e `dashboard/page.tsx` perderam os arrays hardcoded
(`INITIAL_NOTIFICATIONS`, `MOCK_SPARK`, `MOCK_CHANGE`) e passaram a usar `useCarregar` +
`api.dashboard.kpis()`/`api.notificacoes.list()`. Marcar como lida e otimista (aplica na hora,
sincroniza depois) — decisao deliberada pra nao esperar round-trip numa interacao de clique.

**Tentativa de verificar no navegador, sem sucesso** — registrado em
[[preview-do-app-nao-le-documents]]: nesta sessao (extensao VSCode, sem ferramenta de
preview/navegador) o `next dev` rodado via `npm run dev &` num subshell ficou preso em 0% CPU
mesmo depois de forcar `brctl download node_modules`; rodando o Bash tool direto no comando (sem
subshell manual) o servidor respondeu em 130ms — mas depois disso um `curl localhost:3000` **trava
sem erro nenhum**, sem completar. Network loopback do Bash tool parece bloqueado nesta config.
Verificacao final ficou em: `dotnet build`/testes de contrato reais contra os novos endpoints (o
que prova o backend) + `tsc`/`eslint` limpos no front (o que prova que compila) — **sem
confirmacao visual de que a tela renderiza certo**, avisado explicitamente ao Jonas em vez de
alegar "testado".

**Verificacao**: backend 494 unit + 206 contract + 42 functional passando (0 falhas novas).
Frontend tsc/lint limpos, 0 erros novos.

## Fase 8 — pasta "Sem Título" (BtechPlus.Gabriel) avaliada e portada (2026-09-16, noite)
Jonas adicionou ao workspace uma pasta (`~/Documents/Repos/BTech/Sem Título`, nome real do repo
`BtechPlus.Gabriel` — terceira implementação paralela do sistema, de outro dev, NestJS+TypeORM+
SQL Server no backend e Next.js no front) e pediu pra avaliar tudo e ver o que copiar. Inventário
completo em [[BtechPlus.Gabriel]] — achados principais: single-tenant (sem `IdTenant` em lugar
nenhum), login compara senha em texto puro e loga a senha no console (regressão grave, não copiar).
Mas 3 achados valiosos: (1) a tabela `ArqMorto` que o Gabriel usa pra auditoria **já existe
scaffoldada e tenant-aware no BTech.NFe.Api**, só nunca teve service; (2) padrão de exportação
Excel pronto e simples (xlsx + file-saver); (3) template de impressão de Pedido completo.

Depois do "copie o que é necessário e crie os PR", implementado:
- **Auditoria de verdade** (não existia em nenhum projeto — regra 26): `AuditoriaActionFilter`
  (ASP.NET Core) audita toda mutação HTTP com sucesso automaticamente, grava em `ArqMorto` via
  `AuditoriaService`. Corrigidos os 2 problemas do original: `await`+log de erro (não
  fire-and-forget mudo) e tenant-aware de verdade. `GET /api/auditoria` (policy SuperUsuario) +
  tela `/dashboard/auditoria` com click-to-filter. Ver [[26-trilha-de-auditoria]] pro que ainda
  falta (login/logout, falhas, diff de campo, IP) — não é a regra 26 completa, é um avanço real.
- **Excel export** nos 8 cadastros do Web.
- **Impressão de Pedido**.
- **NÃO portado**: `fecharPedido` (fechar pedido → gera Nota Fiscal automaticamente) — vocabulário
  de status conflita com a trava ABERTO/FECHADO/CANCELADO já decidida nesta mesma sessão; fica
  registrado como pendência de decisão, não implementado sem o Jonas escolher.

**Achado no meio do caminho, sem relação com o pedido em si**: entre a Fase 7 e esta, o Jonas
mergeou os PRs #35 (API) e #18 (Web) — eu continuei trabalhando na mesma branch local sem perceber
(3 rodadas de código em cima de uma branch com PR já fechado). Pego antes do push, corrigido
recriando a branch a partir do main atualizado via `git stash`. Ver
[[branch-de-pr-ja-mergeado-acumula-trabalho-novo-sem-perceber]] — inclui outro achado no processo,
arquivos fantasma `page 2.tsx` (duplicata de sincronização do iCloud, conteúdo idêntico, descartados).

**Verificação**: backend 500 unit + 210 contract + 42 functional passando. Frontend tsc/lint/
`npm audit --audit-level=high` limpos (0 vulnerabilidades mesmo com xlsx novo — instalado do
tarball oficial da SheetJS, não do pacote do registry do npm que tem CVE sem correção).

PRs: [B-Tech-Sistemas/BTech.NFe.Api#36](https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/36)
+ [B-Tech-Sistemas/BTech.Web#19](https://github.com/B-Tech-Sistemas/BTech.Web/pull/19) — mergear
juntos, o Web depende das rotas novas da API.

## Relacionado
- [[26-trilha-de-auditoria]]
- [[BTechPLUS.FrontEnd]]
- [[BTech.Web]]
- [[BTech.NFe.Api]]
- [[BtechPlus.Gabriel]]
- [[icloud-evicta-node-modules-e-tsc-trava]]
- [[nullable-enable-transforma-coluna-legada-em-campo-obrigatorio]]
- [[camelcase-id-fields-sem-fronteira-de-palavra]]
- [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]]
- [[campo-ncm-do-produto-era-codfiscal-e-nem-salvava]]
- [[preview-do-app-nao-le-documents]]
- [[branch-de-pr-ja-mergeado-acumula-trabalho-novo-sem-perceber]]
</content>
