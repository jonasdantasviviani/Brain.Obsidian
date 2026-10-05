---
tipo: decisao
titulo: Eventos como fonte da verdade no nucleo Ordem/Jornada
projeto: [Heavy]
stack: [dotnet, postgresql]
tags: [tipo/decisao, stack/dotnet, stack/postgresql]
palavras-chave: [evento, event sourcing, append-only, auditoria, offline, sync, idempotencia, ordem, jornada]
origem: claude-code
criado: 2026-01-01
atualizado: 2026-09-07
confianca: alta
---

# Eventos como fonte da verdade no nucleo Ordem/Jornada
## Resumo
Guardar a sequencia de eventos, nao so o estado atual — tabela append-only + estado materializado, sem framework de event sourcing.

## Contexto
Decisao do [[Heavy]] (doc 07), derivada do doc 02: *"o estado da vida ao sistema"*.

## Detalhe

### O que se ganha de graca
- **Auditoria** de quem mudou o que e quando — necessaria para disputa de canhoto e para defesa
  fiscal/trabalhista
- **Sync offline resolvido pelo mesmo mecanismo**: o app manda eventos com id gerado no cliente
  (idempotencia) e timestamp local + de recebimento; o servidor ordena e reconcilia
- **Alimentacao natural do analitico**: o fluxo de eventos ja e o pipeline
- Reconstrucao de "como estava a operacao as 14h de terca" sem gambiarra

### O que NAO se faz
Nao usar framework de event sourcing puro.
**Uma tabela de eventos append-only + estado materializado resolve**, e e muito mais simples de
operar.

## Relacionado
- [[Heavy]]
- [[separar-operacional-de-analitico]]
- [[monolito-modular-em-vez-de-microservicos]]
