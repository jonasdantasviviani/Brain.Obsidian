---
tipo: sessao
titulo: Tela única de nota (produto, serviço, devolução, importação), número/XML da nota e modal de cancelamento
projeto: [BTech]
stack: [dotnet, nextjs, focus-nfe]
tags: [tipo/sessao, dominio/fiscal, nota-fiscal]
palavras-chave: [tela unica de nota, notas-fiscais/nova, sequencia na query, 404 editar, devolucao, importacao, DI, documentos_importacao, notas_referenciadas, finalidade 4, MovimentarPorNotaAsync, numero da nota, XML, CancelarNotaDialog, faturar abre a nota]
origem: claude-code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: media
---
# Tela única de nota

## Resumo
Uma tela (`/dashboard/notas-fiscais/nova`) com seletor Produto/Serviço/Devolução/Importação, um item de menu só ("Notas Fiscais", serviço é aba da lista), faturar abre a nota nessa tela; API passa a gravar o número da nota, devolução manda `notas_referenciadas` + finalidade 4, importação manda a DI e movimenta estoque.

## Contexto
Pedido do Jonas (01/10): `/notas-fiscais/10132/editar` dava 404; nota de serviço e de produto na mesma tela com seletor; faturar cai nessa tela; nota de devolução (escolher a nota, produtos, saída) e de importação (entra no estoque); XML não baixava; número da nota não aparecia; modal de cancelamento abria no fim da página.

## Detalhe
- **Endereço sem rota dinâmica**: nota gravada abre em `nova?sequencia=N` (NF-e/devolução/importação) ou `nova?tipo=servico&numero=N&serie=S`; helpers e testes em `src/lib/rotasNota.ts`. `[id]/editar`, `nfse/nova` e `nfse/[numero]/[serie]` viraram redirecionamento. Causa real do 404 **não reproduzida** (a rota existe no código e na main); suspeita: servidor `next dev` que não enxergou a pasta nova (repo no iCloud, 18 min para subir). Ver [[checkout-local-atras-da-main-e-ref-duplicada-do-icloud]].
- **Número e XML**: `AtualizarStatusFocusPorRefAsync` só gravava status/chave/protocolo; o `numero` que a Focus devolve nunca ia para `CabNotas.NumeroNota`. Agora grava (emitir, consultar, cancelar, webhook). Na lista, XML/DANFE exigiam `idnfe` e `temNfeProc` (este nem existe na resposta): agora vale `refFocus` + autorizada/cancelada. Nota autorizada antes da correção só ganha número ao "Consultar status".
- **Modal**: `fixed` dentro de ancestral com `animate-slide-up` (transform) deixa de ser relativo à janela — a lista de NF-e usava um modal próprio; trocado por `CancelarNotaDialog` (Dialog em portal).
- **Devolução**: `NfeReferenciada` + `NroNfeRef` (44 dígitos, validador) → Focus `finalidade_emissao=4` e `notas_referenciadas[].chave_nfe`; tela escolhe a nota autorizada (itens, cliente, chave vêm dela; qtde máxima = a da origem), movimento Saída (padrão) ou Entrada; botão "Devolver" na lista.
- **Importação**: `Nfimportacao` + DI (`NumeroDi`, `DataDi`, local/UF/data do desembaraço, `ViaTransporteDi`, `FormaImportacao`, `IdExportador`) → `items[].documentos_importacao[]` com uma adição por item. **Não calcula** II, PIS/COFINS-importação, IOF nem valores aduaneiros (aviso na conferência) — contador.
- **Estoque**: `IEstoqueService.MovimentarPorNotaAsync` (origem "Nota Fiscal" + sequência; repetir não soma) chamado quando a nota de importação ou devolução é autorizada. Venda comum não passa por aqui.
- Pendente de resposta do Jonas: "serviços válidos do banco" no NFS-e — o seletor já consulta `Produtos` tipo S ativos; falta saber se é isso ou a tabela do município da Focus.

## Relacionado
[[produto-e-servico-num-cadastro-so-com-tipo]] · [[2026-09-30-btech-produto-e-servico-unificados]] · [[icloud-evicta-node-modules-e-tsc-trava]]

## Onde está o código (atualizado)
Os worktrees em `_wt/` (iCloud) sumiram durante a sessão com tudo sem commit; o trabalho foi refeito e **commitado** em clones fora do iCloud: `~/Code/nota-web-check` e `~/Code/nota-api-work`, branch `feat/nota-unica-produto-servico-devolucao-importacao` (base origin/main 4ab6069 / 438bb55). Verificado: web tsc limpo, vitest 571, eslint sem erro; API unit 2455 ok (5 NfseEnvioMutacaoTests falham na main limpa), contrato 281 ok. Lição: nunca deixar trabalho sem commit em `_wt` no iCloud; commitar cedo.
