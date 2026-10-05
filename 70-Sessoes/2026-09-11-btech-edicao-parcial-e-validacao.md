---
tipo: sessao
titulo: BTech — PUT parcial, cadastro destravado e validação de CNPJ/CEP
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, aspnetcore, efcore, next, react]
tags: [tipo/sessao, stack/dotnet, stack/react, empresa/btech]
palavras-chave: [400, validacao, put parcial, multi-tenant, IDOR, ViaCEP, CNPJ, CPF, ValidationProblemDetails, importador, notificacoes]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# BTech — PUT parcial, cadastro destravado e validação de CNPJ/CEP
## Resumo
Lista de falhas do Jonas testando o sistema. Virou o PR API #32 e o PR Web #14 (abertos em 2026-09-12, CI verde); importação, seletor de
produto na nota e notificações viraram tarefas paralelas.

## Contexto
Depois do teste dele com um `.bak` real: 400 em cadastro/edição de usuário e empresa, clientes
importados como INATIVOS, CNPJ da empresa não importado, item de nota em texto livre,
notificações fixas, e os pedidos de ViaCEP e validação de campo.

## Detalhe

### API (28 PUTs + criação)
- PUT virou parcial — ver [[put-parcial-sobre-o-registro-existente]]. Corrige de uma vez o 400,
  a perda de dados (senha do usuário, numeração fiscal da empresa) e o PUT em linha de outro tenant.
- Causas do 400 reportado: `IdEmpresaTrab > 0` no `FormulaValidator` (a tela manda 0) e Required
  implícito das colunas legadas — ver
  [[nullable-enable-transforma-coluna-legada-em-campo-obrigatorio]].
- `Empresas.CodEmpresa` não é IDENTITY: `EmpresaService`/`CabPedidoService` geram o próximo código
  (`IRepository.ProximoCodigoAsync`, que ignora o filtro de tenant porque a PK é global).
- Validadores de Cliente/Empresa/Fornecedor: dígito verificador de CNPJ/CPF (`DocumentoFiscal`) e
  CEP obrigatório quando há endereço.
- `GlobalExceptionHandler` parou de devolver `exception.Message` fora de Development/Testing
  (regra 15 das 28) — agora vai `traceId`.
- 731 testes verdes (`dotnet test --filter "Requer!=SqlServer"`), com `EdicaoParcialFlowTests`
  reproduzindo cada tela que falhou.

### Front (worktree `wt-formularios`)
- `api.ts`: erro 400 agora mostra `errors` campo a campo (`mensagemDeErro`), em vez de só o title.
- `lib/documentos.ts` (máscara + DV de CNPJ/CPF), `lib/cep.ts` (ViaCEP), `lib/validacaoCadastro.ts`,
  componentes `CampoCnpjCpf` e `CampoCep` (busca endereço ao completar 8 dígitos).
- Aplicados em ClienteForm, EmpresaForm e nas 4 páginas novo/editar de cliente e empresa.
- CSP: `connect-src` ganhou `https://viacep.com.br` (sem isso o navegador bloqueia sem mensagem).
- `npm run lint` 0 erros, `tsc` ok, `next build` ok.

### Armadilha do ambiente
Worktree em `/private/tmp` com `node_modules` **symlink** para o repo: o Turbopack recusa
("Symlink is invalid, it points out of the filesystem root"). `cp -al` (hard link) resolve e é rápido.

## Pendências
- 3 chips paralelos: importador (mapear coluna por nome do EF — `CNPJ_CPF` ≠ `CnpjCpf` —, status
  'A'/'I' e trazer pedidos/notas/estoque/financeiro), seletor de produto no item da nota,
  notificações reais.
- Tela de usuário manda `email`, mas `Formulas` não tem coluna de e-mail — o campo não grava nada.
- Duas branches do front ainda sem commit: `refactor/carregamento-sem-setstate-em-efeito` e
  `fix/emissao-lote-sem-simulacao`.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[put-parcial-sobre-o-registro-existente]]
