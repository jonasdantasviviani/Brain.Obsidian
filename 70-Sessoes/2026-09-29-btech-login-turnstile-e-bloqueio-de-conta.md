---
tipo: sessao
titulo: Login — bloqueio por conta e Turnstile opcional
projeto: BTech
stack: [dotnet, nextjs, cloudflare-turnstile]
tags: [seguranca, login, turnstile, rate-limit]
palavras-chave: [turnstile, bloqueio de conta, lockout, login, enumeracao de usuarios, CSP, LoginTentativas, desbloquear]
origem: claude-code
criado: 2026-09-29
atualizado: 2026-09-29
confianca: media
---
PRs: BTech.NFe.Api #54 e BTech.Web #36 (branch sec/login-turnstile-e-bloqueio-de-conta). Regras [[11-rate-limit-no-login]] e [[12-bot-protection]].
- Bloqueio por conta: tabela global `LoginTentativas` (migração 029), 5 falhas/15 min -> bloqueio 15 min, config `LoginBloqueio:*`, `TimeProvider` p/ testes. Sempre 401 com o mesmo texto (inexistente, senha errada, bloqueada), sem Retry-After; BCrypt "falso" para igualar o tempo. Tentativa durante o bloqueio não prolonga. Desbloqueio: `POST /api/admin/usuarios/{usuario}/desbloquear` (SistemaAdmin).
- Turnstile: `ITurnstileService`, HttpClient "Turnstile" 5 s, fail closed (503). Chave vazia ou placeholder `__X__` = desligado. Checado antes da senha, não conta como falha da conta.
- Front: `TurnstileWidget` (render explícito, reset após tentativa), botão desabilitado sem token, CSP com challenges.cloudflare.com em script/frame/connect (sempre, pois NEXT_PUBLIC é do build e CSP da subida).
- Armadilhas: a main tinha o teste `NfseEmissaoServiceMontarTests` sem compilar (construtor novo); macOS `sed -i` exige `''`; contexto sem tenant precisa de `IgnoreQueryFilters` ao semear Formula nos testes.
- Não testado: Cloudflare real, SQL Server (migração 029), TypeScript/browser (sem node_modules). Risco: chave secreta na API sem chave de site no front trava o login de todos.
