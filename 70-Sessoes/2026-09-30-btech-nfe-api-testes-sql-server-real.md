---
tipo: sessao
titulo: "BTech.NFe.Api: testes em SQL Server real com Testcontainers"
projeto: [BTech.NFe.Api]
stack: [dotnet, xunit, testcontainers, sqlserver]
tags: [tipo/sessao, testes]
palavras-chave: [testcontainers, migracoes, modelo ef, sql server real, ci]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# Testes em SQL Server real (PR 61)
Projeto `tests/BTech.NFe.Tests.SqlServer`: migrações (em branco, 2x, objetos derivados dos scripts), SELECT TOP 0 por entidade, fluxos multi-tenant. 10/10 no Mac arm64 (amd64 emulado). Job `sqlserver-tests` no CI. Sem Docker: pula (CI=true: falha).
Ver [[update-forca-idtenant-mas-original-value-move-linha-de-outro-tenant]], [[docker-do-banco-nao-sobe-no-mac]], [[BTech.NFe.Api]].
Pendência: `NfseEmissaoServiceMontarTests` quebra o build da solução na main (falta `httpClientFactory`).
