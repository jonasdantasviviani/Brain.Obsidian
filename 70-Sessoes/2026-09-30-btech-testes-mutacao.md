---
tipo: sessao
titulo: "BTech: frente MUTAÇÃO — Stryker.NET e Stryker JS, testes que matam mutantes, cobertura e 3 bugs achados"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, xunit, stryker-net, stryker-js, vitest, coverlet]
tags: [tipo/sessao, testes, mutacao, cobertura, nfse, fiscal]
palavras-chave: [stryker, mutacao, mutantes sobreviventes, NfseCatalogo Lazy, disable String, coverlet, NfseEmissaoService, ProntidaoFiscalService, SessaoService, EstoqueService, vitest fake timers]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-10-01
confianca: alta
---

# BTech: frente MUTAÇÃO

## Resumo
API PR #84 e Web PR #64 (branches `test/mutacao`). Retomada depois do limite de uso: o que estava sujo foi commitado e enviado primeiro. API: Stryker.NET (`dotnet-stryker` local, `stryker-config.json`, workflow `mutacao.yml` semanal+manual) sobre NotaFiscalEmissaoService, PedidoFaturamentoService, NfseEmissaoService, NfseCatalogo, ProntidaoFiscalService, SituacaoTributariaLegado, NormalizacaoLegado e DocumentoFiscal: todos >= 82% (a maioria 90 a 100%), partindo de 2 a 58%. Web: Stryker JS sobre 9 arquivos de `src/lib`, todos >= 90%. Ver [[BTech.NFe.Api]] e [[BTech.Web]].

## Detalhe
- **Rodar**: `cd tests/BTech.NFe.Tests.Unit && dotnet dotnet-stryker --config-file ../../stryker-config.json` (~7 min; `-m '**/Services/X.cs'` para um arquivo). Web: `npm run test:mutacao` (~4 min).
- **Armadilha Lazy/estático**: dado montado uma vez por processo (`NfseCatalogo` via `Lazy`) NÃO é afetado pelo mutante depois da primeira chamada; o score ficava em 5% mesmo com teste de contrato. Solução: o teste chama a fábrica privada por reflexão.
- **Armadilha `// Stryker disable`**: no escopo da classe só vale para o membro seguinte; tem de ser a primeira linha dentro do corpo de cada método. Usei `disable String` só nas fábricas de rótulos do catálogo (centenas de literais de tela) e fixei chaves/tipos/obrigatoriedade num instantâneo (`NfseCatalogoContratoTests`).
- **Armadilha `double` em arredondamento**: 1,004 + 2,001 = 3,00499... dá 3,00, não 3,01. O teste estava errado, não o código.
- **Bugs reais**: (1) `MapearPagamento` com `SomenteDigitos`, que devolve o texto original sem dígito: forma "dinheiro" ia como código inválido e a Focus dava 422; (2) mensagem do total da NFC-e usava a cultura do servidor (ponto no contêiner, vírgula local); (3) Web `mensagemDaResposta` devolvia '' em vez de null.
- **Testes sem relógio/rede**: ViaCEP é `HttpMessageHandler` falso e o cache de CEP é estático (CEP novo por teste); datas de certificado sempre em 2001 ou 2099; vitest usa `vi.useFakeTimers()` + `advanceTimersByTimeAsync`.
- **Cobertura Unit (coverlet.collector)**: 59,4% -> 60,4% das linhas (o total inclui DTOs/Startup/controllers que só a Integration exercita). Testes novos: EstoqueService, SessaoService, PreenchimentoIbgeClienteService.
- **Ainda sem teste**: FocusNfeService (88 linhas), DistribuicaoDfeJob, BackupRestoreService, PerfilService, ParametrosNfseService, DashboardPainelService, XmlContabilidadeService.
- **Disco**: ~4 GB livres; apaguei bin/obj/StrykerOutput e node_modules dos worktrees de mutação ao terminar (reinstalar com `npm ci` / `dotnet restore` para voltar a rodar).
