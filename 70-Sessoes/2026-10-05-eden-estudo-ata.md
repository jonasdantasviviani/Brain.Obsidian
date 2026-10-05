---
tipo: sessao
titulo: Eden - concluir estudo gera a ATA
projeto: Eden
stack: [dotnet, nextjs, ollama, efcore]
tags: [eden, estudos, ata]
palavras-chave: [ata, concluir estudo, reabrir, evidencias, quarentena]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

# Concluir estudo → ATA (PR #43, empilhado sobre #42)

- `POST /v1/studies/{id}/conclude` e `/reopen`; `AtaComposer` (Domain) monta a ata por código, `AtaSummarizer` pede ao modelo local só o "o que aprendi" com `refs` numéricas; `VerifyLearned` descarta pontos com refs inexistentes (sem modelo → fallback com trechos verificados).
- Ata vira nota `_Ata.md` em quarentena (aprovar grava no vault); concluído = somente leitura (409 em instrução/reestudo/RunDue) até reabrir.
- Validado no estudo real da Focus (9 fontes, 13 afirmações) e em 375px.
- Ligado a [[Eden]]. Padrão: IA só resume o que já tem citação verificada; refs checadas em código.
- Armadilha: criar branch de `main` quando o código necessário está em PR aberto — empilhar na branch do PR (`git checkout -B ... feat/x`). Script python com `assert` antes de gravar evita meia-edição.
