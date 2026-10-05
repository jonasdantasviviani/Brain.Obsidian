---
tipo: projeto
titulo: BtechPlus.Gabriel
projeto: [BtechPlus.Gabriel]
stack: [nestjs, typeorm, mssql, next, react, typescript]
tags: [tipo/projeto, stack/nestjs, stack/next, empresa/btech, dominio/fiscal]
palavras-chave: [gabriel, nestjs, typeorm, sem titulo, arqmorto, auditoria, fecharpedido, xlsx, jspdf, printpedido, gestaovendas]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: alta
---

# BtechPlus.Gabriel
## Resumo
Terceira implementação paralela do sistema BTech, feita por outro dev (Gabriel, a julgar pelo nome
do repositório) em NestJS + TypeORM + SQL Server no backend e Next.js 16 + React 19 + shadcn no
frontend. Achada em `~/Documents/Repos/BTech/Sem Título` (nome da pasta local não bate com o nome
do repo — o Jonas adicionou ao workspace sem renomear a pasta) — clone vazio da mesma origem
também existe em `~/Documents/Repos/Pessoal/BtechPlus.Gabriel` (0 commits, provavelmente um clone
que falhou).

## Contexto
`github.com/B-Tech-Sistemas/BtechPlus.Gabriel` · 2 commits (`Initial commit`, `V1`) · sem `.env`
versionado. Achado a pedido do Jonas em 2026-09-16 ("avalie ela, veja se temos algo pra copiar pra
API ou pro Web") — comparado contra [[BTech.NFe.Api]] (oficial, multi-tenant) e [[BTech.Web]]
(oficial, ativo).

## Detalhe

### Diferença arquitetural crítica — NÃO copiar a arquitetura toda
Este backend é **single-tenant**: toda query SQL é hardcoded contra
`[BTechPLUSTESTE].[dbo].[...]` (nome de banco fixo no texto da query, em TODO service), sem
qualquer coluna ou filtro de `IdTenant`. Isso é o oposto do que [[BTech.NFe.Api]] já construiu com
cuidado (`ITenantScoped`, filtro global por tenant — ver [[repositorio-generico-e-servicebase]]).
Também sem repositório genérico: cada service (`ClientesService`, `PedidosService`, etc.) escreve
SQL cru com concatenação de parâmetros posicionais (`@0, @1...`), duplicando lógica de
corte/formatação de string (`cut()`, `formatTipoEmpresa()`) que o EF Core + FluentValidation do
[[BTech.NFe.Api]] já resolve de forma genérica. Entidades TypeORM em `entities/*.entity.ts` estão
**vazias** (`export class Cliente {}`) — o TypeORM ali não mapeia nada, é só usado como pool de
conexão bruto via `DataSource.query()`.

### Achado de segurança — NÃO copiar, é regressão
`auth.service.ts` faz login comparando usuário/senha em **texto puro** direto no SQL
(`WHERE RTRIM([Usuario]) = @0 AND RTRIM([Senha]) = @1` contra a tabela legada `Formulas`) e ainda
**loga usuário e senha no console** (`console.log('Senha: "${senha}"')`). O
[[BTech.NFe.Api]] já tem `PasswordService` com BCrypt — isso aqui é uma regressão de segurança
grave, mantida só como registro do que existe, nunca para portar.

### O que vale a pena olhar (ideia/padrão, não o código literal)

1. **`ArqMorto` já existe pronto no [[BTech.NFe.Api]], sem uso** — a tabela legada de auditoria
   que este backend usa (`backend/src/audit/*`) **já está scaffoldada** em
   `BTech.NFe.Domain.Entities.ArqMorto` (`Sequencia/DataOcorrencia/HoraOcorrencia/Usuario/Tabela/
   Funcao/Descricao/Justificativa`), já marcada `ITenantScoped` (`ArqMorto.Tenant.cs`) — e
   **ninguém nunca escreveu um service/controller pra ela**. Isso muda a estimativa da regra 26
   (trilha de auditoria, sem dono em nenhum projeto — ver [[26-trilha-de-auditoria]]): a parte de
   armazenamento já existe, falta só a camada de aplicação.
   - Padrão do `AuditInterceptor` (NestJS) vale como referência arquitetural: um interceptor
     global que audita toda mutação (POST/PUT/PATCH/DELETE) automaticamente, lendo `x-usuario` do
     header (mesma convenção do [[BTechPLUS.FrontEnd]]) e tentando extrair um "nome do alvo"
     (`Nome`/`Razao`/`Descricao`/`Fantasia`) pra descrição humana — bom pra não esquecer de auditar
     endpoint novo. Portar como `IAsyncActionFilter` no ASP.NET Core, mas **corrigir**: aqui o
     insert é fire-and-forget dentro de um `tap()` sem `await`/`catch` (erro de escrita cai no
     silêncio) e sem `IdTenant` (teria que vir do claim do usuário autenticado, não existe conceito
     de tenant aqui).
   - `HistoricoTable.tsx` (frontend) tem uma UX boa pra copiar: clicar numa célula (usuário/tabela/
     função) já filtra por aquele valor, busca livre por texto em todos os campos, dropdowns de
     filtro rápido. Precisa reescrever a parte de dados (usa `axios` direto num IP fixo da LAN
     `192.168.100.89:3001`, não o `api.ts`) mas o layout/interação é reaproveitável pra uma tela de
     Auditoria em [[BTech.Web]].

2. **`fecharPedido` em `pedidos.service.ts` (linhas 453-693)** — implementação completa e real de
   "fechar pedido → gera Nota Fiscal" dentro de uma transação: escolhe CFOP/Natureza de Operação
   por UF do cliente (dentro/fora do estado), insere `CabNotas` + `CorNotas` + `CorNFe_Srv` a
   partir dos itens do pedido, e só então atualiza `CabPedidos.Status`. Vocabulário de status aqui
   é `PENDENTE → VALIDA` (gerou nota) ou `PENDENTE → BAIXADO` (fechado sem nota, campo `Previsto`)
   — o mesmo vocabulário do [[BTechPLUS.FrontEnd]], **não** o `ABERTO/FECHADO/CANCELADO` que
   [[BTech.Web]] já usa (trava de status decidida e testada em 2026-09-16, ver
   [[2026-09-16-btechplus-frontend-comparacao-telas]] Fase 3/4). Portar a ideia (pedido fechado
   gera nota real, numa transação) para [[BTech.NFe.Api]] exige decidir esse conflito de
   vocabulário antes — não é um "copiar e colar". Nota: essa função **não** dá baixa de estoque
   (nome guardado como gap, mas o `fecharPedido` real não cobre isso apesar do nome sugerir).

3. **Exportação Excel — padrão pronto e simples, direto portável**: `*Table.tsx` (Clientes,
   Produtos, Fornecedores, Vendedores, Pedidos, Empresas, TiposPagto, CondPagto,
   GruposProdutos) todos têm um `exportToExcel()` idêntico: mapeia os dados já carregados na tabela
   pra um objeto com cabeçalhos amigáveis em português, `XLSX.utils.json_to_sheet` →
   `XLSX.write(..., {type:'array'})` → `Blob` → `saveAs` (`xlsx` + `file-saver`, sem chamada de API
   nova nenhuma). Fecha a lacuna "exportar Excel nos cadastros" apontada como pendência cross-
   cutting em [[BTech.Web]] desde a Fase 4 — é só adaptar pros campos/casing reais de cada tela.

4. **`utils/printPedido.ts`** — template HTML/CSS completo de impressão de pedido (cabeçalho
   empresa/cliente, tabela de produtos/serviços, totais, linha de assinatura,
   `@media print`, `window.print()`). O layout é bom e reaproveitável pra "impressão de Pedido"
   (gap apontado desde a Fase 3), mas a busca de dados **não** deve ser copiada: usa IP fixo da LAN
   (`192.168.100.89:3001`) e faz 5 chamadas em paralelo incluindo baixar a lista **inteira** de
   clientes/empresas só pra achar um por id no cliente (`.find()`) — reescrever usando o
   `api.pedidos.get()`/itens que o [[BTech.Web]] já tem.

5. **`GestaoVendas.tsx`** — tela guarda-chuva de Pedidos+Notas com: modo `"view" | "edit"` no
   modal de pedido (o "modo Consultar genérico" que ficou como gap), barra de ação baseada em
   seleção (selecionar 1 pedido habilita Editar/Visualizar/Imprimir), e um `fecharModalOpen`
   dedicado pra "Fechar Pedido" com barra de progresso. Confirma que os gaps de UX apontados nas
   Fases 3/4 do [[BTech.Web]] (modo Consultar, action bar por seleção, workflow guiado de Fechar
   Pedido) já foram resolvidos de algum jeito por outro dev — vale olhar como referência de
   interação, não de código (mesmo problema de IP fixo/axios direto do resto do projeto).

### Não avaliado a fundo (mencionar se for preciso depois)
`notas-fiscais.service.ts` (274 linhas), `vendedores/empresas/fornecedores/grupos/tipos-pagto`
services (mesmo padrão de SQL cru do `clientes.service.ts`, não lidos um a um),
`PedidosEditModal.tsx`/`PedidosDetails.tsx`/`NotasDetails.tsx`/`NfcTable.tsx` (frontend).

### O que foi de fato portado (2026-09-16, tarde)
- **Auditoria via ArqMorto** — portado como conceito, reescrito do zero em C#
  (`AuditoriaActionFilter` + `AuditoriaService` + `AuditoriaController` em [[BTech.NFe.Api]];
  tela `/dashboard/auditoria` em [[BTech.Web]]). Corrigidos os dois problemas apontados: grava com
  `await`/log de erro (não fire-and-forget mudo) e é multi-tenant (`ArqMorto` já era
  `ITenantScoped`, só precisava do service usar isso). PR
  [B-Tech-Sistemas/BTech.NFe.Api#36](https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/36).
- **Exportação Excel** — padrão copiado quase 1:1 (xlsx + file-saver, dados já carregados →
  planilha), adaptado pros 8 cadastros do Web com os campos/casing reais. PR
  [B-Tech-Sistemas/BTech.Web#19](https://github.com/B-Tech-Sistemas/BTech.Web/pull/19).
- **Impressão de Pedido** — layout HTML/CSS copiado, busca de dados reescrita (API real por id, sem
  IP fixo de LAN, sem baixar lista inteira só pra achar um registro).
- **NÃO portado**: `fecharPedido` (gera Nota Fiscal ao fechar o pedido) — vocabulário de status
  conflita com o que o Web já trava (`PENDENTE/VALIDA/BAIXADO` vs `ABERTO/FECHADO/CANCELADO`),
  fica pendente de decisão. Arquitetura single-tenant e login em texto puro seguem como "não
  copiar, só registro".

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[BTechPLUS.FrontEnd]]
- [[26-trilha-de-auditoria]]
- [[2026-09-16-btechplus-frontend-comparacao-telas]]
- [[branch-de-pr-ja-mergeado-acumula-trabalho-novo-sem-perceber]]
