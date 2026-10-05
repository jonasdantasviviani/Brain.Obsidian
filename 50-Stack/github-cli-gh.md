---
tipo: stack
titulo: GitHub CLI (gh)
projeto: [todos]
stack: [git, github]
tags: [tipo/stack, stack/git]
palavras-chave: [gh, github cli, pull request, pr, auth, token, keychain, brew]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-11
confianca: alta
---

# GitHub CLI (gh)
## Resumo
Instalado via Homebrew em 07/09/2026 para abrir pull requests; precisa de `gh auth login` uma vez.

## Detalhe

### Estado
- `gh` 2.100.0 em `/opt/homebrew/bin/gh`
- **Autenticado em 2026-09-11** na conta `jonasdantasviviani` (keyring), protocolo HTTPS.
  Usado para criar os repos da organizacao RabiscoGames. O push do git funciona por outro caminho (`credential.helper = osxkeychain`),
  que o `gh` nao reaproveita.

### Autenticar (uma vez)
```bash
gh auth login          # escolher GitHub.com > HTTPS > autenticar pelo navegador
gh auth status         # conferir
```

### Limite que aparece em toda sessao com agente
O Claude **nao manuseia tokens** — nem os que ja estao no keychain. Entao ele nao roda
`gh auth login --with-token` nem monta header `Authorization` com credencial extraida.
Depois que voce autentica uma vez, ele usa o `gh` normalmente.

### Alternativa sem autenticar
URL de compare com titulo e corpo pre-preenchidos:
```
https://github.com/OWNER/REPO/compare/BASE...BRANCH?expand=1&title=...&body=...
```
Cabe ~8 KB de URL. Abre a pagina do PR pronta, faltando so clicar em "Create pull request".

### Script pronto desta sessao
`~/.claude/cerebro/abrir-prs.sh` cria os 4 PRs da branch `security/checklist-20-regras`,
com os corpos em `~/.claude/cerebro/prs/`.

## Relacionado
- [[2026-09-07-correcao-das-pendencias-de-seguranca]]
