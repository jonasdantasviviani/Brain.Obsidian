---
tipo: sessao
titulo: Pedido com serviços prestados e faturamento misto NF-e + NFS-e (API #79 + Web #58)
projeto: BTech
stack: [dotnet, nextjs]
tags: [pedido, nfse, faturamento, migracao]
palavras-chave: [PedidosServicos, PedidoServico, faturar pedido, NFS-e, CabNFe_Srv.NumeroPedido, PedidoFaturamentoService, idempotencia, consolidacao de servicos, nota manual]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Pedido com serviços e faturamento misto

- Pedido pedido pelo Jonas: poder faturar o mesmo pedido em NF-e dos itens e NFS-e dos serviços, e ter nota manual de produto e de serviço.
- **Modelo**: migração `034`: tabela nova `PedidosServicos` (PK `IdTenant+IdPedido+Sequencia`, Sequencia = posição 1..n decidida pelo servidor, sem IDENTITY) com cópia dos dados fiscais do serviço (item da lista, CNAE, cTribNac, alíquota) e `CodServico` nulo = digitado livre; coluna `CabNFe_Srv.NumeroPedido` (+ índice) para o vínculo pedido -> NFS-e. A `CorPedidosServico` do legado (serviço preso a produto/animal) ficou intocada de propósito. Ver [[documento-com-itens-no-mesmo-corpo]] e [[put-parcial-sobre-o-registro-existente]].
- **API**: `servicos` no mesmo corpo do pedido (ausente preserva, presente substitui, lista vazia apaga), totais `ValorProdutos`/`ValorServicos`/`ValorBruto`/`ValorLiquido` no servidor. `PedidoFaturamentoService` (novo) saiu do `CabPedidoService.FaturarAsync`: gera NF-e (só se há itens) e NFS-e (só se há serviços) como rascunho, SEM transmitir; cada nota volta com `envioAutomatico` e o front emite. Idempotente procurando pelo número do pedido em `CabNotas.NumeroPedido` e `CabNFe_Srv.NumeroPedido` (segunda chamada = 200 com as notas; repetição após falha no meio aproveita a NF-e já gravada). `NfseSalvarRequest.NumeroPedido` é `[JsonIgnore]` para o corpo nunca definir o vínculo.
- **Limitação**: NFS-e tem um serviço por nota; vários serviços viram UMA nota (valores somados bruto, desconto em `desconto_incondicionado`, descrições concatenadas, dado fiscal do primeiro serviço que o tem).
- **Recusas antes de gravar**: pedido vazio, serviço sem empresa, serviço sem módulo `nfse` (claim). Falta de tomador/município/item da lista vira pendência em `nfse.pendencias`.
- Armadilha evitada: apagar e reinserir a mesma chave (pedido, posição) no mesmo SaveChanges colide no rastreamento do EF (repositório lê AsNoTracking): atualiza as posições existentes, adiciona as novas e remove as que sobraram.
- **Web**: `lib/pedidoServicos.ts` e `lib/faturamentoPedido.ts` (lógica pura testada), `components/pedidos/ServicosPedido.tsx`, `SeletorServico`, `BotoesNovaNota`; "Somar vários serviços" no `EditorNfse`.
- Formato de `PedidoFaturadoResponse` mudou (`{nfe?, nfse?, avisos}`): API e Web precisam subir juntos.
- Não feito: SqlServer tests (sem SQL Server aqui), serviços avulsos em `/api/pedidos/{id}/servicos`, NFC-e no faturamento do pedido.
