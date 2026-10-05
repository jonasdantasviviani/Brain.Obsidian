---
tipo: projeto
titulo: BTechPLUS.FrontEnd
projeto: [BTechPLUS.FrontEnd]
stack: [next, react, typescript, tailwind, radix, react-hook-form, zod]
tags: [tipo/projeto, stack/next, empresa/btech, tipo/frontend, status/pausado]
palavras-chave: [btechplus, frontend pausado, v0, cadastros, clientes, fornecedores, vendedores, produtos, condpagto, tipospagto, grupos, pedidos, notas-fiscais, auditoria]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: media
---

# BTechPLUS.FrontEnd
## Resumo
Front-end paralelo/anterior ao [[BTech.Web]], iniciado no v0.dev e pausado; tem cadastros (CRUD) que o Jonas considera prontos e bons, a serem avaliados para portar ao BTech.Web.

## Contexto
`~/Documents/Repos/Pessoal/BTechPLUS.FrontEnd` · `github.com/B-Tech-Sistemas/BTechPLUS.FrontEnd` ·
branch `main`, **um unico commit** ("Start") — sem historico util, `package.json` ainda se chama
`my-v0-project` (originado no v0.dev da Vercel).

No workspace do Jonas os tres projetos aparecem juntos: BTech API ([[BTech.NFe.Api]]), BTech Web
([[BTech.Web]], oficial) e BTech Front End (este repo, pausado). Api e Web devem ficar 100%
integrados; este repo serve so como banco de pecas para recuperar telas/funcionalidades boas.

## Detalhe

### Stack
Next.js 16.0.10 + React 19.2, Tailwind 4, Radix UI (sem shadcn explicito, mas mesma familia de
componentes), `react-hook-form` + `zod`/`yup`, `@tanstack/react-table`, `recharts`, `sonner`,
`axios`. Exportacao: `jspdf`/`jspdf-autotable` (PDF) e `xlsx` (Excel) — o [[BTech.Web]] nao tem
essas duas libs. Tambem lista `mssql` e `typeorm`/`@nestjs/typeorm` como dependencias (suspeito:
resquicio do template v0 ou uma tentativa de acesso direto a banco no cliente — **verificar antes
de assumir que e usado**, pois seria um risco serio se o frontend abrir conexao com SQL Server
diretamente do browser/servidor Next em vez de passar pela API).

### Estrutura (rotas em `app/`)
```text
app/auditoria/
app/clientes/
app/condpagto/        <- sem equivalente encontrado ainda no BTech.Web
app/empresas/
app/fornecedores/      <- sem equivalente encontrado ainda no BTech.Web
app/grupos/            <- (grupos de produto) sem equivalente encontrado ainda no BTech.Web
app/login/
app/notas-fiscais/
app/pedidos/
app/produtos/
app/tipospagto/        <- sem equivalente encontrado ainda no BTech.Web
app/vendedores/        <- sem equivalente encontrado ainda no BTech.Web
```
Componentes de tela ficam em `components/` (nao colocalizados com a rota): `ClientesForm.tsx`,
`ClientesTable.tsx`, `CondPagtoForm.tsx`, `CondPagtoTable.tsx`, `EmpresasForm.tsx`,
`EmpresasTable.tsx`, `FornecedoresForm.tsx`, `FornecedoresTable.tsx`, `GruposProdutosForm.tsx`,
`GruposProdutosTable.tsx`, `NfcTable.tsx`, `PedidosDetails.tsx`, `PedidosEditModal.tsx`,
`PedidosTable.tsx`, `ProdutosForm.tsx`, `ProdutosTable.tsx`, `TiposPagtoForm.tsx`,
`TiposPagtoTable.tsx`, `VendedoresForm.tsx`, `VendedoresTable.tsx`, mais `sidebar.tsx`,
`theme-provider.tsx`, `components/auditoria/`, `components/ui/`.

### Inventario completo de telas (levantado por agente Explore em 2026-09-16)

**Achado critico de arquitetura**: nao existe cliente API central (`lib/api.ts`) — cada componente
importa `axios` direto e hardcoda a base URL `http://192.168.100.89:3001` (IP de maquina local, sem
`.env`). Ou seja, este front nao falava com a `BTech.NFe.Api` (.NET); falava com uma **API Node/Nest
separada** (o `package.json` lista `@nestjs/typeorm`, `typeorm`, `mssql` como dependencia, mas nao ha
codigo de servidor neste repo — provavel repo irmao NestJS que definia o contrato real de rotas tipo
`/clientes/check-documento/:doc`, `/pedidos/:id/fechar` etc. Vale procurar se esse repo Nest ainda
existe em algum lugar, pois ele documenta o contrato de campos com mais precisao que este front).
Login (`app/login`) faz `POST /auth/login {usuario, senha}`, guarda a resposta crua em
`localStorage.user_session`, sem JWT/refresh/401-interceptor — so checagem de presenca no
`ClientLayout.tsx`. Todo request mutante carrega header `x-usuario` para auditoria server-side.

**Padrao "Cadastro" identico em 8 modulos** (Clientes, Empresas, Fornecedores, Vendedores, Produtos,
GruposProdutos, CondPagto, TiposPagto): 1 `XxxTable.tsx` (lista, TanStack Table, busca, paginacao
20, export Excel via `xlsx`+`file-saver`, duplo-clique = ver) + 1 `XxxForm.tsx` (mesma tela alterna
list/create/edit/view via state, header azul INCLUINDO/ALTERANDO/CONSULTANDO, validacao `yup`,
mascara/Mod-11 de CPF/CNPJ duplicada em 4 arquivos, autofill de endereco via ViaCEP no blur do CEP,
checagem de documento duplicado antes de salvar). Isso e claramente o que o Jonas quis dizer com
"cadastros que ja funcionavam bem" — consistente e completo em UI/UX mesmo sem poder testar contra
API viva.

**Telas dedicadas (todas ligadas a API real, sem mock/TODO)**:
- **Clientes** `/clientes` — implementacao de referencia do padrao; secoes Dados Gerais / Endereco
  (com botao "ver no Google Maps") / Informacoes Fiscais (Simples/Isento/RPA, Final/Revenda).
- **Empresas** `/empresas` — mesmo padrao + CRT (regime tributario), Email, Obs.
- **Fornecedores** `/fornecedores` — o form mais rico do padrao: `TipoForne` (Fornecedor **ou
  Transportadora** — mesma tabela cobre as duas), EmailXML (entrega de XML de NFe), Site,
  IDCodigoIBGE auto-preenchido pelo ViaCEP. Checagem de duplicidade aqui e feita client-side
  (busca a lista inteira e faz `.find()`) em vez de endpoint dedicado como os outros.
- **Vendedores** `/vendedores` — cadastro simples (comissao %, nascimento); a mesma tabela e
  reaproveitada dentro de Pedidos tanto como "Vendedor" quanto como "Prestador".
- **Produtos** `/produtos` — form mais complexo (~1100 linhas): calculo de preco **ao vivo**
  (PrecoVenda = custo + custo*margem/100 a cada tecla, com par independente de margem/preco para
  Revenda), modais de busca reutilizaveis para Grupo/Fornecedor/Marca/Classificacao Fiscal
  (`/produtos/aux/clfiscal` auto-preenche Situacao Tributaria + Aliq. IPI ao selecionar), bloco
  condicional de ICMS. Comentarios no codigo revelam renomeacoes recentes de campo
  (BaselCMS->BaseICMS, CodFiscal->ClassificacaoFiscal, SitTrib->SituacaoTributaria,
  DataAjustePreco->DataTabelaPreco) — sinal de que o schema da API estava mudando quando pausou.
- **Grupos de Produtos** `/grupos` — minimo (CodGrupo, Descricao, Ativo).
- **Condicao de Pagamento** `/condpagto` — construtor de parcelamento com **12 campos Dias1..Dias12**
  (dias ate vencimento por parcela).
- **Formas de Pagamento** `/tipospagto` — `PadraoNFe` (codigo SEFAZ de 2 digitos, 01=Dinheiro..17=PIX
  ..99=Outros) e `BandeiraCartao`, cada um com modal "?" mostrando **tabela de referencia oficial
  SEFAZ hardcoded** clicavel para preencher — peca pronta e reaproveitavel como esta.
- **Pedidos (Pedido de Venda)** `/pedidos` — a tela mais avancada do app, workspace master/detail
  (nao e so CRUD): grade com 20+ colunas coloridas por status (PENDENTE verde, BAIXADO preto,
  FATURADO azul, VALIDA roxo, CANCELADO vermelho); "Fechar Pedido" (`PATCH /pedidos/:id/fechar`)
  pede PREVISTO/NAO PREVISTO e **gera saida de estoque, atualiza saldo dos produtos e muda status
  para VALIDA/BAIXADO** — logica de negocio real de fechamento de pedido, nao existe nada parecido
  nos outros modulos; so pedido PENDENTE pode ser cancelado/alterado; modal de edicao full-screen
  com busca de Empresa/Cliente/Vendedor/Prestador/Forma/Condicao via `/pedidos/busca/{tipo}`,
  desconto bidirecional (R$ <-> %) recalculado ao vivo; impressao em 2 formatos (A4 e matricial
  50 colunas) com tabelas de Forma/Condicao de pagamento **hardcoded e duplicadas** entre as duas
  funcoes de impressao — bom candidato a conferir contra a API antes de portar.
- **Emissao Nota Fiscal** `/notas-fiscais` — tela fina: so lista com status (Pendente/Autorizado) e
  botao "Enviar nota" (`POST /notas-fiscais/enviar/:id`, texto do toast cita "Focus NFe"). Sem
  visualizacao de itens, sem DANFE/XML, sem cancelamento/inutilizacao — parece so um painel de
  monitoramento/disparo, o pedido/faturamento gera a nota em outro lugar.
- **Auditoria** `/auditoria` (fora do menu principal, botao proprio no rodape da sidebar) — log de
  `GET /audit` com INCLUSAO/ALTERACAO/EXCLUSAO/ESTORNO/BAIXA/UNIFICAR, busca + filtros dropdown, e
  **clique na celula (Usuario/Tabela/Funcao) aplica o filtro** — UX boa de reaproveitar.
- **Login** `/login` e Home `/` (esta e so um placeholder de boas-vindas, sem logica).

**Sidebar (`components/sidebar.tsx`) mostra o escopo pretendido, boa parte nunca implementada**:
Relatorios, Financeiro (Debito/Credito/ContasCorrentes/FluxoCaixa cairiam aqui — zero implementado,
nenhuma referencia no codigo), Agendamentos e Estoque sao todos so `"Em breve"` sem rota real —
apesar de Produtos ter EstMin/EstMax e o fechamento de Pedido alegar mexer em estoque, nao ha tela
de estoque nenhuma (sem ajuste manual, sem historico de movimentacao, sem recebimento de compra).
Botao "Configuracoes" na sidebar existe mas **nao faz nada** (sem onClick). Tambem nao existe em
lugar nenhum: AliquotasIcm, CstIcm/CstIpi/CstPisCofin, NaturezaOperacao como cadastro proprio
(ClFiscal so aparece como lookup somente-leitura dentro do form de Produtos).

**Codigo morto**: todo o kit shadcn/ui em `components/ui/` + `theme-provider.tsx` +
`hooks/use-toast.ts`/`use-mobile.ts` nunca sao importados — as telas reais foram feitas na mao com
Tailwind cru + lucide-react + `sonner`. Sem dark mode de fato apesar de `next-themes` estar instalado.

**Refactor obvio se for portar**: os validadores/mascaras de CPF/CNPJ/telefone/CEP estao copiados
quase identicos em 4 arquivos (`ClientesForm`, `EmpresasForm`, `VendedoresForm`,
`FornecedoresForm`) — extrair para um `lib/validators.ts`/`lib/masks.ts` unico valeria a pena junto
com a portabilidade.

### Gap-analysis fechado
Comparativo linha a linha contra [[BTech.Web]] concluido — ver
[[2026-09-16-btechplus-frontend-comparacao-telas]] para a tabela de gaps e prioridade sugerida
(Fornecedores > Vendedores+CondPagto > TiposPagto > GruposProduto).

## Relacionado
- [[BTech.Web]]
- [[btech-nfe-web]]
- [[BTech.NFe.Api]]
</content>
