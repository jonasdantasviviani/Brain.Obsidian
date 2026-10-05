---
tipo: sessao
titulo: BTech — "failed to fetch" ao rodar local e scripts/README para Windows
projeto: [BTech.NFe.Api, BTech.Web]
stack: [docker, next, powershell, dotnet]
tags: [tipo/sessao, empresa/btech]
palavras-chave: [failed to fetch, rodar local, windows, powershell, dev-local, setup-local, run-local-api, env.example, csp, hsts, bom, pr fechado]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# BTech — "failed to fetch" ao rodar local e scripts/README para Windows
## Resumo
Causa do dia: Docker Desktop parado. Corrigidas mais quatro causas do mesmo sintoma e criado o caminho Windows/macOS para subir API + front; sistema rodando local e validado pelo navegador.

## Contexto
Continuacao de [[2026-09-11-btech-nfe-api-importador-sem-vazar-credencial]]. Antes: fechado o
**Web #9** (branch antiga `security/checklist-20-regras` republicada por engano num push meu das
12:44; `git cherry` mostrou os dois commits ja na main).

## Detalhe
**PRs abertos** (a pedido do Jonas): API **#20** (`5a2d035`, CI verde, sem conflito) e Web **#10**
(`fe0fe3f`, CI vermelho so pelo `npm audit` pre-existente — o lint nem roda). Antes de abrir,
testado de verdade o `setup-local.sh` com o Docker Desktop **fechado** (`osascript -e 'quit app
"Docker Desktop"'`): abriu o Docker e a stack voltou em ~20 s — eu tinha aberto o Docker a mao no
primeiro teste e quase afirmei no PR algo que nao tinha testado.

Mudancas (branch `fix/execucao-local-windows` nos dois repos):
- Web: `apiFetch` com mensagem clara, padrao `http://localhost:5204`, CSP com o mesmo padrao, HSTS
  so em producao, `.env.example` (+ excecao no `.gitignore`), `.gitattributes`,
  `scripts/dev-local.{sh,ps1}`, README reescrito em pt-BR. `AGENTS.md` alterado pelo `next dev`.
- API: `setup-local.*` abrem o Docker Desktop; `run-local-api.*` na 5204 + Development + checa
  porta; BOM nos 4 `.ps1`; `.gitattributes`; INSTALLATION (secao do front, Windows, troubleshooting).

Validacao: volume legado copiado para `btech-sqlserver-data-backup-legado` (230 MB) antes do
`down -v`; `setup-local.sh` subiu banco em branco + Administrador; `dev-local.sh` subiu o front;
no navegador: CSP libera a API, sem HSTS em dev, `/health` 200, login com corpo vazio 400 (preflight
ok). Nao digitei a senha no formulario (regra de nao inserir senha) — login pela UI fica com o Jonas.
`tsc` (TS 7) e `next build` ok; `npm run lint` e `npm audit` falham na main (pre-existente).
Parser do `pwsh` sem erros nos 5 `.ps1`. Detalhes: [[failed-to-fetch-no-front-btech]],
[[powershell-5-1-le-ps1-sem-bom-como-windows-1252]], [[preview-do-app-nao-le-documents]].

Depois (a pedido): **CI do Web corrigido no PR #11**, mergeado pelo Jonas — `npm audit fix`
(9 vulns, 5 high, so lockfile), TS `^5.9.3`, ESLint `^9`, `ignore` no Dependabot, 24 erros de lint
corrigidos no codigo (os tipos reais acharam 2 tooltips que quebrariam com `undefined`) e
`set-state-in-effect` como warn. Main do Web verde pela primeira vez. Feito numa worktree no
scratchpad (fora do iCloud) para nao mexer no front que estava rodando. Refatoracao dos 47 efeitos
virou tarefa sugerida. Ver [[dependabot-mergeado-com-ci-vermelho-quebra-a-main]].

Depois: "conflitos nos PRs pendentes" = #17/#18/#19 do Dependabot (agrupados), em conflito porque o
Jonas mergeou os equivalentes por projeto (#22/#25/#29/#27/#30). Fechados como substituidos com
comentario. Os 5 restantes (#21, #23, #24, #26, #28) verificados **juntos** numa worktree: merge sem
conflito, build sem avisos, Unit 434 / Integracao 42 / Contract 199 / Functional 28. Web sem PRs.

## Relacionado
- [[BTech.Web]]
- [[BTech.NFe.Api]]
- [[failed-to-fetch-no-front-btech]]
