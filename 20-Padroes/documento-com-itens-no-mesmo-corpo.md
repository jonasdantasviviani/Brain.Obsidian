---
tipo: padrao
titulo: "Documento com itens: cabeçalho e linhas no mesmo corpo, gravados numa transação só"
projeto: [BTech.NFe.Api]
stack: [dotnet, aspnetcore, efcore, sqlserver]
tags: [tipo/padrao, aspnetcore, efcore, api]
palavras-chave: [documento com itens, cabeçalho e itens, NotMapped, SaveChanges, transação, totais no servidor, AtualizarParcialAsync, antesDeSalvar, IDENTITY]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Documento com itens: cabeçalho e linhas no mesmo corpo

## Quando usar
Entidade escalfoldada de schema legado (sem coleção de navegação, sem FK com cascade) que a tela
trata como **um** documento: pedido, nota fiscal, entrada.

## Como
1. `[NotMapped] List<TItem>? Itens` num partial separado (`<Entity>.Itens.cs`), para sobreviver a
   um re-scaffold. `NotMapped` porque quem grava é o serviço — o EF não deve inventar navegação
   nem FK no schema legado.
2. `GetByIdAsync` preenche `Itens`, ordenado pela sequência.
3. `CreateAsync` adiciona cabeçalho e itens ao mesmo `DbContext` e chama **um** `SaveChangesAsync`:
   o EF embrulha em transação, então não existe documento salvo pela metade.
   Quando a PK do cabeçalho é IDENTITY, salva o cabeçalho primeiro (os itens precisam do id).
4. `UpdateAsync`: `Itens == null` preserva o que está gravado; lista presente **substitui**
   (apaga e reinsere) — a tela manda a lista inteira, e casar item a item exigiria um id que ela
   não tem.
5. `DeleteAsync` apaga os itens junto: sem FK com cascade, item órfão reaparece quando o número
   do documento é reaproveitado.
6. **Totais são decisão do servidor.** `valorTotal` do item é recalculado (`qtde × unitário`) e
   os totais do cabeçalho saem dos itens. O que o navegador mandou serve no máximo de conferência.

## No controller (PUT parcial)
`AtualizarParcialAsync` só aplica escalares de propósito. Os itens são lidos direto do
`JsonElement` e entregues via `antesDeSalvar`:

```csharp
var itens = LerItens(corpo);   // null quando o corpo não traz "itens"
return await this.AtualizarParcialAsync(
    atual, corpo,
    e => _service.UpdateAsync(e, ct),
    [nameof(CabPedido.NumeroPedido)],
    antesDeSalvar: e => e.Itens = itens);
```

## Cuidado com `Sequencia`
No schema legado ela é IDENTITY **da tabela inteira**, não do documento: um pedido pode ter itens
3, 4 e 5. Zere antes de inserir (SQL Server recusa escrita em identity, erro 8102) e, em tela,
numere pela posição na lista — usar a sequência do banco como chave da lista colide com o item
novo, que nasce como `length + 1`.

Nasceu de [[entidade-sem-colecao-descarta-itens-no-binder]].
