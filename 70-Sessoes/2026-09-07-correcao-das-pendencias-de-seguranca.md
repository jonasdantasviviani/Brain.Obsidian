---
tipo: sessao
titulo: Correcao das pendencias de seguranca nos 5 projetos
projeto: [BTech.NFe.Api, BTech.Web, Heavy, ICook, btech-nfe-web, Sites]
stack: [dotnet, next, html, seguranca]
tags: [tipo/sessao, stack/dotnet, stack/next, seguranca]
palavras-chave: [seguranca, correcao, pendencia, dependabot, headers, https, hsts, honeypot, automapper, vulnerabilidade, error boundary, csp]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Correcao das pendencias de seguranca nos 5 projetos
## Resumo
25 pendencias viraram 11: dependabot nos 5 repos, security headers, HTTPS/HSTS, honeypot em 6 sites e a remocao de um pacote vulneravel.

## Contexto
07/09/2026, continuacao de [[2026-09-07-20-regras-de-seguranca]].
Tudo verificado com build e teste — nada foi dado como feito sem compilar.

## Detalhe

### O achado que mudou o rumo da sessao
`dotnet list package --vulnerable` na [[BTech.NFe.Api]] apontou **AutoMapper 14.0.0, severidade
High** (CVE-2026-32933, DoS por recursao). Nao ha correcao na linha 14.x — so 15.1.1+, que e
onde o AutoMapper virou licenca comercial.

Antes de forcar o upgrade, investiguei o uso real:
27 `CreateMap<X, X>()` **todos identidade**, **zero** injecoes de `IMapper`.
Era **dependencia morta** — e alguem ja havia silenciado o aviso com `NoWarn NU1903`
(ver [[suprimir-aviso-de-vulnerabilidade-com-nowarn]]).

Remover resolveu sem upgrade e sem questao de licenca:
build 0 avisos · **392 testes passando** · `--vulnerable` limpo em toda a solucao.

### O que foi feito, por regra

| Regra | Onde | O que |
| --- | --- | --- |
| **20** scan de dependencias | 5 repos | `dependabot.yml` (nuget/npm/pub/actions) |
| **20** | BTech.NFe.Api, ICook | passo de scan no `ci.yml` existente |
| **20** | BTech.Web, btech-nfe-web | `seguranca.yml` novo com `npm audit --audit-level=high` + cron semanal |
| **18** headers | Heavy | `MiddlewareDeCabecalhosDeSeguranca` no topo do pipeline |
| **18** | ICook | middleware inline no `Program.cs` (CSP `default-src 'none'` — API so devolve JSON) |
| **18** | 2 apps Next | `headers()` no `next.config.ts` + `poweredByHeader: false` |
| **19** HTTPS | Heavy, ICook | `UseHsts()` fora de dev + `UseHttpsRedirection()` |
| **19** | 2 apps Next | `Strict-Transport-Security` nos headers |
| **15** nao vazar | 2 apps Next | `error.tsx` + `global-error.tsx` com mensagem generica e `digest` |
| **12** bot | 6 sites estaticos | [[honeypot-anti-bot-em-formulario]] |
| **02** secrets | ICook | `.gitignore` passou a cobrir `.env` em qualquer pasta |

### Verificacao
- `dotnet build` BTech.NFe.Api: **0 avisos, 0 erros** · 392 testes unitarios verdes
- `dotnet build` Heavy: **0 avisos** com `TreatWarningsAsErrors` ligado
- `dotnet build` ICook: **0 avisos** com `-warnaserror`
- Honeypot: testado no navegador servindo por HTTP — invisivel, fora do Tab,
  bot descartado, humano passa
- YAML de todo dependabot e workflow validado com parser

### O que NAO deu para verificar
`npm run build` dos dois projetos Next **nao completou neste ambiente**: o processo `next build`
fica com ~0% de CPU, sem produzir uma linha de saida, com ou sem `CI=1` e
`NEXT_TELEMETRY_DISABLED=1`. Nao e falha do codigo — nenhum erro e emitido.

O que **foi** verificado nesses dois projetos:
- `tsc --noEmit` limpo em `next.config.ts`, `error.tsx` e `global-error.tsx`
- As mudancas sao declarativas: config de headers e os dois componentes de erro padrao do
  App Router

**Pendente de confirmacao:** rodar `npm run build` e `npx next start`, e conferir os headers
com `curl -I http://localhost:3000`. Ate la, a regra 18 nesses dois projetos esta *escrita*,
nao *provada*.

### Armadilha da propria sessao
Testei o honeypot abrindo o HTML via `file://` e conclui que estava **visivel**.
Era falso: o preview carrega como `data:` URL e o `href="style.css"` relativo nao resolve —
**zero folhas de estilo carregadas**. So servindo por HTTP (`python3 -m http.server`) o teste
virou real. Ver [[testar-html-local-sem-servidor-engana]].

### O que NAO foi feito, e por que
Ver [[pendencias-de-seguranca-que-exigem-decisao]].

## Publicacao

Branch `security/checklist-20-regras` em cada repositorio, com push:

| Repo | Commits | Push |
| --- | --- | --- |
| [[BTech.NFe.Api]] | `29a97aa` seguranca · `ceab6c3` config local | ok |
| [[BTech.Web]] | `4f495aa` headers + error boundaries | ok |
| [[Heavy]] | `756937c` middleware + HSTS | ok |
| [[ICook]] | `a710421` seguranca · `89b889d` scaffold Android | ok |
| [[btech-nfe-web]] | `8422ee2` seguranca · `c068cf5` CRLF | **sem remote** — so local |

Escolha de branch em vez de commit direto na main: as mudancas de headers nos dois projetos
Next nao puderam ser validadas em runtime, entao a main fica intacta ate a revisao.

### Confirmacao externa do achado
O push da [[BTech.NFe.Api]] veio com um aviso do proprio GitHub:

> GitHub found 1 vulnerability on B-Tech-Sistemas/BTech.NFe.Api's default branch (1 high)

E o AutoMapper. A main ainda tem; a branch ja nao tem. Confirmacao independente do que a
auditoria local havia encontrado.

### Descoberta ao commitar
O [[btech-nfe-web]] estava com 16 arquivos alterados **so em quebra de linha** (1814
insercoes e 1814 delecoes, diff vazio com `--ignore-all-space`). Commitado separado do
resto. Ver [[repositorio-oscilando-entre-crlf-e-lf]].

## Relacionado
- [[Checklist-Seguranca]]
- [[repositorio-oscilando-entre-crlf-e-lf]]
- [[suprimir-aviso-de-vulnerabilidade-com-nowarn]]
- [[honeypot-anti-bot-em-formulario]]
- [[pendencias-de-seguranca-que-exigem-decisao]]
