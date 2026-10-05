---
tipo: sessao
titulo: BTech.NFe.Api - 5 testes NfseEnvioMutacaoTests falhando na main (em andamento)
projeto: [BTech]
stack: [dotnet, git, icloud]
tags: [tipo/sessao, stack/dotnet]
palavras-chave: [NfseEnvioMutacaoTests, NfseEmissaoService, Update duas vezes, worktree origin/main, git fetch lento]
origem: claude-code
criado: 2026-10-01
atualizado: 2026-10-01
confianca: media
---
## Resumo
Pedido: worktree novo de origin/main na BTech.NFe.Api e investigar 5 testes falhando (Update chamado 2x em vez de 1) — sessão ainda em andamento.

## Contexto
Testes em tests/BTech.NFe.Tests.Unit/Mutacao/NfseEnvioMutacaoTests.cs. Hipótese: reserva da referência antes de transmitir (PRs #83/#85) faz o serviço chamar Update duas vezes de propósito.

## Detalhe
- Até aqui só rodei `git status`/`git fetch origin main`, que estouraram 120 s e foram para background (disco iCloud), como já registrado em [[checkout-local-atras-da-main-e-ref-duplicada-do-icloud]].
- Ainda sem edições; falta criar worktree, ler NfseEmissaoService, decidir lado correto, rodar suíte e abrir PR.

## Relacionado
[[checkout-local-atras-da-main-e-ref-duplicada-do-icloud]]
