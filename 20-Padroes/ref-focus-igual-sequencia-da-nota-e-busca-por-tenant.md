---
tipo: padrao
titulo: Ref da Focus = Sequencia da nota (única só por tenant) e a busca por ref
projeto: [BTech.NFe.Api]
stack: [dotnet, focus-nfe, multitenant]
tags: [tipo/padrao, dominio/fiscal]
palavras-chave: [ref, refFocus, sequencia, webhook, tenant, focus, nfe]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

# Ref da Focus = Sequencia

Pedido do Jonas: a `ref` NF-e/NFC-e deve ser o campo `Sequencia` da nota (antes `nfe-{emp}-{seq}-{timestamp}`).
`Sequencia` é única só dentro do tenant (PK composta com IdTenant), então a busca por ref sem tenant (webhook) pode achar
várias notas: o webhook consulta cada candidata com o token da própria empresa e fica com a que a Focus reconhece e cuja chave
confere (`ListarPorRefFocusSemTenantAsync` + `AtualizarStatusFocusDaNotaAsync`); `AtualizarStatusFocusPorRefAsync` prefere a
nota do tenant da requisição. Risco residual: duas empresas no mesmo token Focus com a mesma Sequencia colidem na própria Focus.
NFS-e continua com `nfse-t{tenant}-e{emp}-{numero}-{serie}`. Ver [[BTech.NFe.Api]].
