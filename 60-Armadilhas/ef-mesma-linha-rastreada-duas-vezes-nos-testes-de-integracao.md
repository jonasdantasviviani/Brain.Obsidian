---
tipo: armadilha
titulo: Teste com um DbContext só: "another instance with the same key is already being tracked"
projeto: [BTech.NFe.Api]
stack: [dotnet, efcore, xunit]
tags: [tipo/armadilha, stack/dotnet]
palavras-chave: [efcore, rastreamento, FindAsync, AsNoTracking, Update, testcontainers, DbContext por requisição]
origem: claude-code
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---

## Resumo
Os repositórios leem com `AsNoTracking` e gravam com `Update/Remove`: tocar a mesma linha duas vezes no mesmo `DbContext` estoura.

## Detalhe
Na API cada requisição tem seu contexto, então o fluxo funciona. Em teste de integração que encadeia importar → baixar → cancelar no mesmo contexto,
o EF recusa a segunda instância da mesma chave (Debito, EmpresasXestoque). Solução: um contexto por operação no teste (helper `ComAsync`), como a API.
Achados no mesmo trabalho: `Movimentacoes.Sequencia` é IDENTITY (inserir na mão falha); `AdicionarComCodigoSequencialAsync` precisa participar da transação já aberta;
data só-data (`2026-10-01`) o JS lê como UTC e vira o dia anterior em fuso negativo (use `T00:00:00`).
Relacionado: [[ef-update-de-entidade-sem-rastreamento-com-a-mesma-ja-rastreada]], [[nota-de-entrada-por-xml-analisar-e-importar]]. PRs BTech.NFe.Api#96/#97, BTech.Web#72/#73.
