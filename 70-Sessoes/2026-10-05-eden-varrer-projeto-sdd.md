---
tipo: sessao
titulo: Eden - Varrer projeto gera SDD e abre PR
projeto: [Eden]
stack: [dotnet, nextjs, postgres]
tags: [tipo/sessao, stack/dotnet, stack/nextjs]
palavras-chave: [varrer projeto, SDD, as-built, Repo.AddSdd, specs/000-visao-geral, eden/add-sdd, pagina do projeto]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: media
---

## Resumo
"Varrer projeto" (Configurações · GitHub) agora gera o SDD as-built, guarda no banco para a página do projeto e propõe PR draft com `specs/000-visao-geral/{spec,plan,tasks}.md`.

## Contexto
Antes, "Varrer agora" só deduzia o `eden.yaml`. Jonas pediu que varresse, criasse o SDD no git via PR e que o SDD alimentasse a página de detalhes do projeto.

## Detalhe
- Domínio: `SddGenerator` (determinístico, sem LLM): FR por componente, AC por comandos test/build/lint, tarefas T para lacunas. README entrou em `RepoProfiler.WantsContent`.
- Nova ação `Repo.AddSdd` (multi-arquivo, só `specs/<pasta>/spec|plan|tasks.md`, branch `eden/*`); `CommitFilesAndOpenPrAsync` generalizou o commit de arquivo único. Mantém a aprovação em Aprovações (Constituição §3).
- Coluna `repos.SddJson` (migration `RepoSdd`, criada com `ConnectionStrings__Eden=... dotnet ef migrations add --no-build` após `dotnet build`).
- API: `POST /v1/repos/{id}/profile?proposeSdd=true`, `GET /v1/projects/{id}/sdd`. Web: widget "SDD do projeto" + preview em Aprovações. Contrato regenerado (`UPDATE_CONTRACT=1 dotnet test --filter OpenApiContract`, `npm run gen`).
- Testes: SddGeneratorTests (domínio, 211 ok) e RepoProfileTests/ActionsTests/OpenApi (26 ok); tsc/eslint limpos. Suíte .NET completa ainda rodando ao encerrar.
- Pendente: nada commitado nem PR aberto ainda.

## Relacionado
[[Eden]]
