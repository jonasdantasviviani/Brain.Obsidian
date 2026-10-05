---
tipo: padrao
titulo: Repositorio generico + ServiceBase para CRUD
projeto: [BTech.NFe.Api]
stack: [dotnet, csharp, efcore]
tags: [tipo/padrao, stack/dotnet]
palavras-chave: [repositorio, repository, generico, servicebase, crud, chave composta, efcore, paginacao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Repositorio generico + ServiceBase para CRUD
## Resumo
`IRepository<T>` + `ServiceBase<TEntity, TKey>` cobrem o CRUD; chave composta exige sobrescrever dois metodos.

## Contexto
Padrao da [[BTech.NFe.Api]], onde o modelo EF Core e **scaffolded do banco existente**.

## Detalhe

### `IRepository<T>`
`GetAllAsync` · `GetPagedAsync` · `GetByIdAsync` · `FindAsync` · `AddAsync` · `Update` ·
`Remove` · `SaveChangesAsync`

### `ServiceBase<TEntity, TKey>`
Services herdam dele e ganham o CRUD pronto.

**Para entidade de chave composta e obrigatorio sobrescrever `GetByIdAsync` e `DeleteAsync`.**
Caso contrario a busca por id nao encontra nada e o delete nao apaga — falha silenciosa.

Casos conhecidos no projeto:

| Entidade | Chave |
| --- | --- |
| `CorNota` | `IdNota + Sequencia` |
| `CorPedido` | `Idpedido + Sequencia` |
| `CorEntrada` | `Identrada + Sequencia` |
| `FormulaService` | PK e `Usuario`, **nao** `CodUsuario` |

### Paginacao
Filtros MVC globais `PaginationHeaderFilter` e `PaginationValidationFilter` cuidam do header e
da validacao — nao reimplementar por controller.

## Relacionado
- [[BTech.NFe.Api]]
- [[migrations-sql-manuais-em-vez-de-ef-migrations]]
