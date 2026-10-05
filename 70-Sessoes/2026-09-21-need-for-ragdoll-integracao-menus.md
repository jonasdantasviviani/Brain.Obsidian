---
tipo: sessao
titulo: Need for Ragdoll - integracao dos menus, garagem, recordes e progresso salvo
projeto: [RagdollGames]
stack: [flutter, dart, flame]
tags: [tipo/sessao, jogo/ragdoll, menu, garagem, progresso]
palavras-chave: [menu, modos, garagem, recordes, progresso, apagar progresso, cosmeticos]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: media
---

# Integracao dos menus do Need for Ragdoll

- Correr abre modos (campeonato ancora, corrida rapida, treino livre); Garagem com miniaturas via `CarroComponent.avulso`; Recordes; Ajustes com dificuldade e Apagar progresso (dois toques).
- Decisao: dificuldade mora no `Progresso`, nao em `Ajustes` (evita dois donos do mesmo dado). Ver `docs/decisoes/integracao-menus.md` no repo.
- Logica pura em `lib/application/menus` (registrador, texto das novidades, rodizio, pistas pelo id), testada; widgets so analisados (Flutter nao roda no ambiente do workflow).
- Ver [[Need-for-Ragdoll]] se existir a nota do projeto.
