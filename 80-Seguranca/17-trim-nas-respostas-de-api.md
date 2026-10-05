---
tipo: seguranca
titulo: 17. Trim nas respostas de API
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [dto, resposta, over fetching, entidade, serializacao, campo, vazamento, select, projecao, api, retornar, listar, endpoint, get, tela, consultar, controller]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 17. Trim nas respostas de API
## Resumo
A API devolve um DTO com exatamente os campos que a tela precisa — nunca a entidade inteira.

## Contexto
Regra 17 das 20 obrigatorias. E a irma de [[08-bloquear-mass-assignment]]: uma cuida da entrada,
esta cuida da saida.

## Detalhe

### O que vaza quando se devolve a entidade
- Colunas internas: `senha_hash`, `token_reset`, flags de sistema, `custo` numa tela de venda
- **Propriedades de navegacao**: devolver `Pedido` traz `Pedido.Cliente`, que traz todos os
  pedidos do cliente. Um `include` distraido vira dump do banco
- Estrutura da tabela — informacao gratis para quem quer atacar

E ainda: qualquer coluna nova no banco aparece **automaticamente** na API, sem ninguem decidir isso.

### O jeito certo
```csharp
public sealed record NotaResumoResponse(int Id, string Numero, DateTime Emissao, decimal Total);

return await ctx.Notas
    .Where(n => n.EmpresaId == empresaAtual)
    .Select(n => new NotaResumoResponse(n.Id, n.Numero, n.Emissao, n.Total))
    .ToListAsync(ct);
```
O `.Select` antes do `ToList` faz o **banco** trazer so essas colunas — seguranca e performance
na mesma linha.

### No seu codigo hoje — achado real
A [[BTech.NFe.Api]] devolve entidades EF direto em varios controllers
(`AliquotasIcm`, `CabEntrada`, `CabNota`, `CabPedido`…). Como o modelo e scaffolded do banco
legado, **cada resposta expoe todas as colunas da tabela**.
O [[Heavy]] ja passa por DTO em tudo — use ele como referencia.

## Relacionado
- [[08-bloquear-mass-assignment]]
- [[15-nao-vazar-dados]]
- [[BTech.NFe.Api]]
