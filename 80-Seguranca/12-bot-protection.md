---
tipo: seguranca
titulo: 12. Bot protection
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [bot, captcha, recaptcha, turnstile, hcaptcha, honeypot, spam, formulario, automacao, scraping, contato, cadastro, landing page, site, lead]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 12. Bot protection
## Resumo
Todo formulario publico tem defesa contra automacao: captcha, Turnstile ou, no minimo, honeypot.

## Contexto
Regra 12 das 20 obrigatorias. Vale para login, cadastro, recuperacao de senha e **formulario de
contato** — inclusive nos [[Sites-Estaticos]].

## Detalhe

### Escolha pelo custo de atrito
| Solucao | Atrito | Quando usar |
| --- | --- | --- |
| **Honeypot** (campo escondido que humano nao preenche) | zero | contato simples, landing page |
| **Cloudflare Turnstile** | quase zero, sem clique | padrao recomendado hoje |
| **reCAPTCHA / hCaptcha** | medio | quando ja existe no projeto |
| **Desafio proof-of-work** | zero visual | APIs publicas |

### O honeypot, que e gratis e resolve muita coisa
```html
<input type="text" name="empresa_fax" tabindex="-1" autocomplete="off"
       style="position:absolute;left:-9999px" aria-hidden="true">
```
Se vier preenchido, e bot. Descarte em silencio (sem mensagem de erro — nao ensine o bot).

### Sempre valide no servidor
O token do captcha tem que ser verificado no backend contra a API do provedor.
Captcha validado so no cliente nao vale nada — mesma logica de [[06-auth-no-servidor]].

### No seu codigo hoje
Nenhum projeto tem protecao contra bot. O caso mais urgente sao os formularios de contato dos
[[Sites-Estaticos]] (spam direto) e o login das APIs.

## Relacionado
- [[11-rate-limit-no-login]]
- [[06-auth-no-servidor]]
