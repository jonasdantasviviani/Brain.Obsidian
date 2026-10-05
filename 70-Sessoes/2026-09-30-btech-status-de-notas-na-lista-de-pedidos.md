---
tipo: sessao
titulo: Status das notas (NF-e/NFS-e) e pendência de envio na lista de pedidos (API #80 + Web #60)
projeto: BTech
stack: [dotnet, nextjs]
tags: [pedido, nfe, nfse, pendencia, listagem]
palavras-chave: [situacao de nota, rascunho, rejeitada, pendente de envio, temNfePendente, notas=pendentes, SituacaoNota, ResumirNotasAsync, NotasDoPedido, useEnvioDeNota]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Status das notas na lista de pedidos

- Pedido do Jonas: ver no pedido se há nota de serviço/produto pendente, botões NF-e/NFS-e na linha, evidenciar "pendente de envio", só para pedido faturado.
- **Situação normalizada no servidor** (`Domain/Models/SituacaoNota.cs`): rascunho | processando | autorizada | rejeitada | cancelada. `StatusFocus` (sem caixa) vence; sem ele vale o `Status` legado (NF-e: VALIDA/CANCELADA/INUTILIZADA/DENEGADA/ERRO/PROCESSANDO; NFS-e: AUTORIZADA/VALIDA/CANCELADA/ERRO/PROCESSANDO); o resto é rascunho. Pendente de envio = rascunho ou rejeitada (erro_autorizacao, erro_validacao, denegado). Ver [[status-comparado-com-caixa-sensivel-trava-botao]].
- **Lote sem N+1**: `PedidoRepository.ResumirNotasAsync` = 2 consultas IN por página (CabNotas.NumeroPedido texto, modelo 55; CabNFe_Srv.NumeroPedido int). Só pedidos FATURADOS entram. Filtro `?notas=pendentes|nfe-pendente|nfse-pendente|sem-nota` em EXISTS no banco; as expressões `NfePendente/NfsePendente` (SQL) e `DaNfe/DaNfse` (C#) precisam concordar: teste unitário cruza as duas em matriz. Ver [[paginacao-no-servidor-com-totais-separados]].
- Vários vínculos: o resumo mostra a pendente (senão a de menor número); os booleanos valem para qualquer uma. NF-e cancelada+reemitida autorizada segue "pendente" enquanto houver rascunho/rejeitada antiga (limitação conhecida).
- `GET /api/pedidos/{id}` traz o mesmo resumo (detalhe). NF-e não tem série gravada (só `Serie_Old`): `serie` pode vir nula.
- Armadilha: `PedidoResponse.De` com parâmetro opcional quebra o uso como method group em `AtualizarParcialComRespostaAsync` (CS0411): usar lambda.
- **Web**: `lib/notasDoPedido.ts` (puro, vitest), `components/pedidos/NotasDoPedido.tsx`, `hooks/useEnvioDeNota.ts` (envio rápido com confirm; exige `notas_fiscais:transmitir`, e módulo `nfse` para NFS-e), `DataTable.rowClassName`. Rotas: `/dashboard/notas-fiscais/{id}/editar` e `/dashboard/nfse/{numero}/{serie}`.
- Não feito: conferência visual no navegador (sem API no ar), envio em lote, NFC-e (modelo 65) no resumo.
