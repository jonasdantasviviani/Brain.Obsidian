---
tipo: padrao
titulo: Importador mostra também o que NÃO importou
projeto: [BTech.NFe.Api]
stack: [dotnet, sqlserver]
tags: [tipo/padrao, importador, ux, onboarding]
palavras-chave: [importador, importacao, bak, visibilidade, escopo, tabelas, inventario, sys.partitions, migracao de dados, onboarding]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Importador mostra também o que NÃO importou

## Resumo
Numa importação de escopo parcial, listar só o que entrou faz o usuário concluir que o resto
também veio. O inventário tem de incluir as tabelas **fora do escopo**, marcadas como tal.

## Contexto
O importador do [[BTech.NFe.Api]] copia 6 tabelas de Cadastros de um banco legado com ~100
tabelas. O painel mostrava dois números (`TotalRegistros`/`TotalErros`) e um log de texto livre —
o Jonas não conseguia responder "a tabela X entrou?", e as 97 tabelas restantes não apareciam em
lugar nenhum. Ausência na tela lê-se como sucesso.

## Detalhe

### O inventário completo, não o recorte
Uma linha por tabela da **origem**, com status explícito:

| Status | Quando |
| --- | --- |
| `Importada` | tudo entrou |
| `Parcial` | entrou, mas alguma linha falhou |
| `Erro` | a tabela inteira falhou |
| `ForaDoEscopo` | existe na origem e não faz parte desta versão |
| `NaoEncontrada` | está no escopo, mas não existe na origem (schema mais antigo) |

`ForaDoEscopo` é o que resolve o problema: a tabela aparece, com quantos registros ficaram para
trás, e a mensagem diz por quê.

### Contagem: exata onde importa, barata no resto
`COUNT(*)` nas tabelas do escopo — é o número que o usuário compara com o resultado. Para o
inventário, o catálogo, numa consulta só:

```sql
SELECT t.name, SUM(p.rows)
FROM sys.tables t
JOIN sys.partitions p ON p.object_id = t.object_id AND p.index_id IN (0, 1)
WHERE t.is_ms_shipped = 0
GROUP BY t.name
```

`index_id IN (0,1)` = heap ou clustered, ou seja, a tabela em si — sem isso cada índice
não-clustered soma as mesmas linhas de novo. A resposta marca qual número é exato e qual é
estimativa, em vez de fingir precisão.

### Registros na origem × importados
Guardar a contagem lida **antes** de importar é o que permite dizer "vieram 118 de 120" em vez
de só "vieram 118". Sem isso, `Parcial` não tem como ser detectado.

### O inventário não pode derrubar o principal
Ler o catálogo é informativo. Se falhar (permissão), o preview das tabelas do escopo continua
valendo — `try/catch` devolvendo lista vazia. Mesma ideia na gravação: o status da execução é
salvo **antes** do detalhe, para que uma falha ao gravar o detalhe não leve junto o
acompanhamento da importação.

## Relacionado
- [[banco-em-branco-com-importacao-por-painel]]
- [[descartar-banco-temporario-no-fim-do-job]]
- [[BTech.NFe.Api]]
