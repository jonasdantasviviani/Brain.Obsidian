---
tipo: armadilha
titulo: CI vermelho por 4 causas escondidas: testes desatualizados, fuso, curva ABC e collation na migração 020
projeto: [BTech.NFe.Api]
stack: [dotnet, sqlserver, xunit]
tags: [tipo/armadilha, stack/dotnet]
palavras-chave: [ci, fuso horario, DateTimeOffset, collation, DATABASE_DEFAULT, sys.tables, curva abc, testcontainers]
origem: claude-code
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---

## Resumo
CI da main vermelho por vários merges seguidos mascarava falhas novas; 4 causas independentes.

## Detalhe
- Testes escritos antes de mudança de comportamento (reserva do envio NFS-e) falham em toda rodada.
- `DateTime.TryParse("...T10:00:00-03:00")` converte para o fuso da máquina: container UTC grava 13h. Use `DateTimeOffset.TryParse(...).DateTime` para horário de parede. Reproduza com `TZ=UTC dotnet test`.
- Migração 020: comparar `sys.tables.name` com variável de tabela dá "Cannot resolve the collation conflict" (catálogo x banco Latin1_General_CI_AS): `COLLATE DATABASE_DEFAULT` nos dois lados.
- Curva ABC: classe pelo acumulado ANTERIOR (< 80% = A); dois clientes iguais são ambos A.
- Docker no Mac: abrir o Docker Desktop (`open -a Docker`) e rodar `dotnet test tests/BTech.NFe.Tests.SqlServer` reproduz os testes de SQL real (Testcontainers).
Relacionado: [[docker-do-banco-nao-sobe-no-mac]], [[dependabot-mergeado-com-ci-vermelho-quebra-a-main]]. PR BTech.NFe.Api#95.
