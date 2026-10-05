---
tipo: seguranca
titulo: 07. Travar acesso aos registros
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [idor, autorizacao, registro, dono, owner, tenant, id sequencial, acesso indevido, filtro, buscar por id, detalhe, consultar registro, get, listar, endpoint]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 07. Travar acesso aos registros
## Resumo
Toda consulta filtra pelo dono do dado. Saber o id de um registro nao pode dar acesso a ele.

## Contexto
Regra 7 das 20 obrigatorias. O nome tecnico e **IDOR** (Insecure Direct Object Reference) e e uma
das falhas mais comuns e mais faceis de explorar: basta trocar o numero na URL.

## Detalhe

### O erro classico
```csharp
// ERRADO: qualquer usuario autenticado le a nota de qualquer empresa
var nota = await _repo.GetByIdAsync(id);
```
```csharp
// CERTO: o dono faz parte da consulta, nao de um if depois
var nota = await _repo.FindAsync(n => n.Id == id && n.EmpresaId == usuarioAtual.EmpresaId);
```

### As tres camadas, da mais forte para a mais fraca
1. **RLS no banco** — cobre ate o SQL cru → [[04-ativar-rls]]
2. **`HasQueryFilter` global no EF Core** — cobre o que passa pelo ORM
3. **Filtro explicito na query** — cobre o caso especifico, e o mais facil de esquecer

Use as tres. Nenhuma sozinha basta.

### Complemento
Prefira id nao sequencial (GUID/ULID) em recurso exposto. Nao e defesa — e reducao de
superficie: sem RLS, um id sequencial vira um enumerador de toda a base.

### No seu codigo hoje
[[Heavy]] tem as tres camadas (RLS + `HasQueryFilter` + filtro na query).
[[BTech.NFe.Api]] tem `HasQueryFilter` — vale confirmar se cobre **todas** as entidades com dono.

## Relacionado
- [[04-ativar-rls]]
- [[multi-inquilino-com-rls-no-postgres]]
- [[06-auth-no-servidor]]
