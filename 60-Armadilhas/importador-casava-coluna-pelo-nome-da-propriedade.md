---
tipo: armadilha
titulo: Importador casava coluna pelo nome da propriedade e descartava dado em silêncio
projeto: [BTech.NFe.Api]
stack: [dotnet, efcore, sqlserver]
tags: [tipo/armadilha, stack/dotnet]
palavras-chave: [importador, legado, CNPJ_CPF, HasColumnName, GetColumnName, IModel, scaffold, migracao de dados, mapeamento]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Importador casava coluna pelo nome da propriedade
## Resumo
`ImportarTabelaAsync` montava o dicionário com `typeof(TEntity).GetProperties()` e casava com o nome da coluna da origem. Toda coluna renomeada no scaffold (`CNPJ_CPF`, `IE_RG`, `IDTabIBGE`, `Natureza_Srv`) caía fora — sem erro, sem log: o CNPJ das empresas simplesmente não vinha.

## Contexto
[[BTech.NFe.Api]], 2026-09-11, importação do `.bak` de cliente.

## Detalhe
- Correção: montar o mapa a partir do modelo do EF, que já conhece os `HasColumnName`:
  ```csharp
  var tipo = context.Model.FindEntityType(typeof(TEntity))!;
  foreach (var p in tipo.GetProperties())
      if (p.PropertyInfo is { CanWrite: true } info)
          { mapa[p.GetColumnName()] = info; mapa.TryAdd(info.Name, info); }
  ```
- A PK também pode ter nome diferente: comparar `prop == pkProp`, não o nome da coluna.
- Dá para testar sem banco: o modelo é construído sem abrir conexão —
  `new NfeDbContext(new DbContextOptionsBuilder<NfeDbContext>().UseSqlServer("Server=x;Database=x").Options).Model`.
- Conferência de cobertura: comparar `INFORMATION_SCHEMA.COLUMNS` da base legada com as colunas
  do modelo. Depois da correção só sobraram, em Clientes, os endereços de entrega 2..7 e `Contato`
  (54+1 colunas que o scaffold não trouxe para a entidade).

## Relacionado
- [[BTech.NFe.Api]]
- [[base-legada-btech-delphi]]
