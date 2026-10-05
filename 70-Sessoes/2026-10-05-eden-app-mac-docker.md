---
tipo: sessao
titulo: Eden - app do Mac que liga o Docker ao abrir
projeto: Eden
stack: [macos, docker, bash]
tags: [eden, instalacao, docker, ollama]
palavras-chave: [Eden.app, install-mac, docker desktop, launcher]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

# Éden.app (PR feat/instalar-mac)

- `scripts/install-mac.sh` monta `~/Applications/Éden.app` (bundle com bash, `LSUIElement`); caminho do repo fica em `~/Library/Application Support/Eden/repo`.
- Launcher: `open -ga Docker` + espera `docker info` → Ollama → `compose up -d` (`--build` só se faltar imagem; vault via `docker-compose.vault.yml`) → espera `:3000/login` → abre navegador. Log em `~/Library/Logs/Eden/launcher.log`.
- Armadilha: app aberto pelo Finder tem PATH mínimo; o launcher exporta `/usr/local/bin`, Homebrew e o bin do Docker.app. Testado com Docker desligado; ligado de novo, é idempotente.
- Ligado a [[Eden]].
