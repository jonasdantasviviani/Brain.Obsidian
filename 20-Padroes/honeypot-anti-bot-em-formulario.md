---
tipo: padrao
titulo: Honeypot anti-bot em formulario
projeto: [Sites, todos]
stack: [html, css, javascript]
tags: [tipo/padrao, stack/html, seguranca]
palavras-chave: [honeypot, bot, spam, formulario, captcha, campo escondido, landing page, contato, anti-automacao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Honeypot anti-bot em formulario
## Resumo
Campo escondido que humano nao ve nem tabula; se vier preenchido, e bot — descarta em silencio.

## Contexto
Aplicado nos 6 formularios dos [[Sites-Estaticos]] em 07/09/2026, para a
[[12-bot-protection]]. Custo zero, sem servico externo, sem atrito para o usuario.

## Detalhe

### As tres pecas

**HTML** — dentro do `<form>`:
```html
<div class="hp-campo" aria-hidden="true">
  <label for="hp-empresa-site">Deixe este campo em branco</label>
  <input type="text" id="hp-empresa-site" name="empresa_site" tabindex="-1" autocomplete="off">
</div>
```

**CSS** — esconder **sem** `display:none`:
```css
.hp-campo { position: absolute !important; left: -9999px !important;
            width: 1px; height: 1px; overflow: hidden; }
```

**JS** — a guarda, no inicio do handler:
```js
const hp = e.target.querySelector('input[name="empresa_site"]');
if (hp && hp.value.trim() !== '') { e.target.reset(); return false; }
```

### Os quatro detalhes que fazem funcionar
1. **Nao usar `display:none`** — os bots mais espertos pulam campos com display:none.
   Posicionar fora da tela engana mais.
2. **`tabindex="-1"`** — humano navegando por teclado nunca cai no campo.
3. **`autocomplete="off"`** — impede o gerenciador de senhas do navegador de preencher
   sozinho e gerar falso positivo contra um usuario real.
4. **Descartar em silencio** — nada de "erro: voce e um bot". Mensagem de erro ensina o bot
   a tentar de novo sem o campo.

### Nome do campo importa
Use um nome plausivel (`empresa_site`, `empresa_fax`), nao `honeypot`. Bot que le o nome do
campo pula os obvios.

### Limite
Honeypot para spam burro. Contra ataque dirigido, some com rate limit
([[11-rate-limit-no-login]]) e, em formulario critico, Turnstile.

### Validado
Testado no navegador servindo por HTTP: campo a `-9999px`, fora da tela, inalcancavel por Tab;
submit com o campo preenchido e descartado, com o campo vazio segue o fluxo normal.

## Relacionado
- [[12-bot-protection]]
- [[Sites-Estaticos]]
