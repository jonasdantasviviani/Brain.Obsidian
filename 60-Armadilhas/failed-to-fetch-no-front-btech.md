---
tipo: armadilha
titulo: "Failed to fetch" no front BTech — cinco causas, uma mensagem
projeto: [BTech.Web, BTech.NFe.Api]
stack: [next, react, dotnet, docker]
tags: [tipo/armadilha, stack/next, stack/docker, empresa/btech]
palavras-chave: [failed to fetch, typeerror, api fora do ar, docker desktop parado, NEXT_PUBLIC_API_URL, env.local, csp connect-src, cors, localhost 127.0.0.1, hsts localhost, porta 5204, porta 8080, run-local-api]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# "Failed to fetch" no front BTech — cinco causas, uma mensagem
## Resumo
O navegador devolve o mesmo `TypeError: Failed to fetch` para API fora do ar, porta errada, CORS, CSP e HSTS — sem status nem corpo. Diagnostico em ordem, do mais comum ao mais raro.

## Contexto
2026-09-11: o Jonas tentou rodar local e o front so mostrava "failed to fetch". Causa do dia:
Docker Desktop fechado (AutoStart desligado) — a API nem estava de pe. Investigando, apareceram
mais quatro formas de chegar no mesmo erro numa maquina nova (tipico no Windows).

## Detalhe
| # | Causa | Como aparece | Correcao feita |
| --- | --- | --- | --- |
| 1 | Docker Desktop parado | `localhost:5204/health` nao responde | `setup-local.{sh,ps1}` abrem o Docker Desktop e esperam ate 2 min |
| 2 | Clone novo sem `.env.local` (fica fora do git) | front chamava o padrao `https://localhost:7000` | padrao virou `http://localhost:5204` em `api.ts`; `.env.example` versionado |
| 3 | CSP `connect-src` montada so com `NEXT_PUBLIC_API_URL` | sem a variavel, o navegador bloqueia a chamada | `next.config.ts` usa o mesmo padrao 5204 |
| 4 | `run-local-api` subia na 8080 | front procura 5204 | padrao 5204 + aviso de porta ocupada (`docker compose stop api`) |
| 5 | Front aberto em `127.0.0.1:3000` | CORS so libera `localhost` | documentado |

Extra: HSTS em `localhost` e guardado **por host, sem porta** — se valesse em dev, forcaria HTTPS
tambem na API local. Agora so em `NODE_ENV=production`.

O `apiFetch` do `src/lib/api.ts` troca o TypeError por: "Nao foi possivel conectar a API em …,
confira se ela esta rodando (abra …/health)".

Diagnostico sem credencial, direto do navegador na origem do front (passa CSP + CORS + rede):
```js
await fetch('http://localhost:5204/health').then(r => r.status)            // 200
await fetch('http://localhost:5204/api/auth/login', {method:'POST',
  headers:{'Content-Type':'application/json'}, body:'{}'}).then(r => r.status) // 400 = preflight ok
```
`/health` responde `Degraded` sem token da Focus NFe — e normal; o que importa e o HTTP 200.

## Relacionado
- [[BTech.Web]]
- [[BTech.NFe.Api]]
- [[docker-do-banco-nao-sobe-no-mac]]
- [[powershell-5-1-le-ps1-sem-bom-como-windows-1252]]
