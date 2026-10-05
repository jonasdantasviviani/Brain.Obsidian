---
tipo: armadilha
titulo: CSP 'self' apaga TODO o texto do Flutter web - a Roboto vem do gstatic
projeto: [RagdollGames, Rabisco-Hub]
stack: [flutter, dart, web, seguranca]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [csp, content-security-policy, connect-src, font-src, gstatic, roboto, fonte que nao carrega, texto invisivel, menu sem texto, canvaskit, fontFallbackBaseUrl, no-web-resources-cdn, regra 18, security headers]
origem: claude-code
criado: 2026-09-22
atualizado: 2026-09-22
confianca: alta
---

# CSP 'self' apaga todo o texto do Flutter web

## Resumo
O jogo abre, desenha, anima - e **nao mostra uma letra**. A CSP do proprio jogo barra a Roboto
que o motor do Flutter busca em `fonts.gstatic.com`, e sem fonte de texto nada e escrito.

## Contexto
Need for Ragdoll, 2026-09-22, build do CI (`e4d927e`) servido pelo hub. A abertura da Rabisco
rodou inteira, o menu desenhou a marca-texto amarela do titulo... e parou ali. Console:

```text
Connecting to 'https://fonts.gstatic.com/s/roboto/v32/KFOmCnqEu92Fr1Me4GZLCzYlKw.woff2'
violates the following Content Security Policy directive: "connect-src 'self' data: blob:"
Failed to load font Roboto at https://fonts.gstatic.com/...
```

Removendo so a `<meta>` de CSP do `build/web/index.html`, o menu aparece inteiro
(Need for Ragdoll / Correr / Garagem / Recordes / Ajustes / Como jogar). **A CSP e a causa.**

## Detalhe

### Por que acontece
- O `pubspec.yaml` do jogo **nao declara nenhuma fonte**; no `lib/` nao ha um `fontFamily`.
  Entao todo texto cai na fonte padrao, que na web e a **Roboto baixada do gstatic em runtime**.
- `flutter build web --no-web-resources-cdn` resolve o **canvaskit**, nao a fonte de fallback.
- A CSP de `web/index.html` (regra [[18-security-headers]], entrou com o CSP do roadmap) traz
  `connect-src 'self' data: blob:` e `font-src 'self' data:` - certissimo para tudo, menos para
  a unica coisa que o motor busca fora.
- O `_headers` repete a mesma politica: **no ar quebra igual**, nao e problema so de local.
- Counter-Ragdoll e Ragdoll GO **nao tem** CSP no build: escrevem texto porque baixam a Roboto
  sem ninguem barrar. Ou seja: aplicar a regra 18 neles quebra os dois do mesmo jeito.

### Como corrigir (no repo do jogo, pede build)
1. **Empacotar a fonte** em `assets/fonts/`, declarar em `pubspec.yaml` e usar como padrao
   (`ThemeData(fontFamily: ...)` / o `TextStyle` base do jogo). Sem fallback, sem gstatic, e
   ainda casa com o traco a mao da serie. E a correcao que mantem a CSP fechada.
2. Ou hospedar o fallback junto e apontar `fontFallbackBaseUrl` na configuracao do motor.
3. **Nao** afrouxar a CSP para `fonts.gstatic.com`: [[Rabisco-Hub]] e a serie nao tem fonte
   externa em lugar nenhum.

### Como conferir rapido
Abrir o jogo e ler o console: `Failed to load font Roboto` + violacao de `connect-src`. Sem
texto na tela e com a animacao rodando, e isso - nao e trava de jogo.

## Relacionado
- [[18-security-headers]]
- [[servidor-do-hub-compila-os-jogos-antes-de-subir]]
- [[2026-09-22-rabisco-hub-need-for-ragdoll-jogavel]]
- [[RagdollGames]]
