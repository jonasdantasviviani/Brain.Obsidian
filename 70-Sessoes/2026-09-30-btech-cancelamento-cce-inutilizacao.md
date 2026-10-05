---
tipo: sessao
titulo: Cancelamento, CC-e e inutilização de numeração (NF-e/NFC-e)
projeto: BTech
stack: [dotnet, nextjs, focus-nfe]
tags: [fiscal, focus, sefaz]
palavras-chave: [inutilizacao, carta de correcao, cancelamento, erro_cancelamento, numero_carta_correcao]
origem: sessao
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Sessão

Branch `feat/cancelamento-cce-inutilizacao` (API + Web). Inutilização não existia; CC-e só na rota legada com token global; cancelamento existia mas gravava `erro_cancelamento` por cima de `autorizado`.

- API: `/api/inutilizacoes` (tabela `InutilizacoesNumeracao`, migração 033), `/api/notas-fiscais/{id}/carta-correcao|cartas-correcao`, cancelamento por modelo (55/65) com validação 15–255.
- Armadilha: a Focus documenta `numero_carta_correcao` na resposta da CC-e, o código lia `numero_sequencia` (nunca vinha) e gravava rejeição como se fosse aceita (`Status != "erro"`, mas o enum é `autorizado|erro_autorizacao`). Ver [[focus-ignora-em-silencio-campo-que-nao-reconhece]] e [[openapi-da-focus-e-subconjunto-use-a-pagina-de-campos]].
- Prazo de cancelamento não é validado localmente (varia por UF; NFC-e 30 min segundo a Focus): a SEFAZ decide e a mensagem volta ao usuário.
- Front: verificar com tsc/lint no clone fora do iCloud (~/Code/BTech.Web) via worktree + symlink de node_modules.
