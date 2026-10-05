---
tipo: padrao
titulo: Hub de jogos web - um caminho por jogo, base href reescrito no servidor local
projeto: [Rabisco-Hub, RagdollGames]
stack: [flutter, html, css, python]
tags: [tipo/padrao, stack/flutter, stack/html, dominio/jogos]
palavras-chave: [hub, subcaminho, subpath, base href, --base-href, flutter build web, servidor local, http.server, translate_path, varios jogos mesmo dominio, link direto, object-fit contain, aspect-ratio, tremor css, feDisplacementMap]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Hub de jogos web - um caminho por jogo

## Resumo
Pagina estatica na raiz aponta para `/<jogo>/`; cada jogo e o proprio build web, compilado com
`--base-href /<jogo>/`, e abre sozinho pelo link. Localmente, um servidor de 100 linhas monta tudo
sem recompilar.

## Contexto
[[Rabisco-Hub]], 2026-09-12. Vale para qualquer conjunto de apps Flutter web (ou SPA com `<base>`)
debaixo de um dominio so.

## Detalhe

### Producao
```bash
flutter build web --release --base-href /counter-ragdoll/
```
O `build/web` vai para `/<jogo>/` do mesmo site; o hub vai na raiz. O manifest usa `start_url: "."`
e o bootstrap resolve tudo pelo `<base>`, entao so o `--base-href` muda.

### Local, sem recompilar (`servir.py`)
- `translate_path`: se o primeiro segmento esta na tupla fechada `JOGOS`, troca `self.directory` para
  `../<jogo>/build/web` e passa o resto do caminho ao `super()` (que ja descarta `..`).
- `send_head`: `index.html` do jogo servido com `<base href="/">` trocado por `<base href="/<jogo>/">`.
  `/<jogo>` sem barra cai no `super()`, que responde `301` para `/<jogo>/` sozinho.
- Sem build: `send_error(404, ..., "Rode flutter build web --release em ../<jogo>")`.
- `extensions_map` com `.wasm: application/wasm` e `.js: text/javascript`.
- `end_headers`: `Cache-Control: no-store` sempre ([[http-server-do-python-serve-build-velho]]);
  CSP/`X-Frame-Options`/`Permissions-Policy` so nas rotas do hub - o jogo decide os dele.
- `functools.partial(Servidor, directory=str(HUB))` evita `os.getcwd()` no construtor.

Conferir com `curl`:
```bash
curl -s -o /dev/null -w '%{http_code} -> %{redirect_url}\n' http://127.0.0.1:5120/ragdoll-go
curl -s http://127.0.0.1:5120/ragdoll-go/ | grep -o '<base href="[^"]*">'
curl -s --path-as-is -o /dev/null -w '%{http_code}\n' "http://127.0.0.1:5120/ragdoll-go/../servir.py"
```

### Cartao do hub
- Capa: caixa com `aspect-ratio: 3/2; position: relative; overflow: hidden` e `<img>` com
  `position: absolute; inset: 0; object-fit: contain`. **`height: 100%` em item de grid dentro de
  `aspect-ratio` nao resolve** - a imagem fica na altura natural e o `overflow` corta.
- Cartao inteiro clicavel: `.botao::after { content: ""; position: absolute; inset: 0 }` com o cartao
  `position: relative` e o texto do link completado por `<span class="sr">`.
- Inclinacao por variavel (`--giro`) para o `:hover` ganhar do `:nth-child` sem briga de especificidade.

### Tremor em CSS puro (identidade Rabisco)
Tres `<filter>` inline com `feTurbulence` (sementes diferentes) + `feDisplacementMap scale="3"`, e
```css
@keyframes tremor { 0% { filter: url(#tremor-1) } 33.333% { filter: url(#tremor-2) } 66.666% { filter: url(#tremor-3) } }
.logo { animation: tremor 300ms step-end infinite; }
```
300 ms / 3 quadros = 10 fps (`tremorFps`). `animation-delay` diferente por cartao para nao tremer em
sincronia. Desligar em `prefers-reduced-motion`.

### Muitos jogos: dados + gerador + filtro em CSS
Com o catalogo inteiro (84 jogos) o HTML deixou de ser escrito a mao - ver
[[hub-gerado-por-script-sem-js]]:
- `dados/jogos.json` -> `gerar.py` -> `site/index.html` + `site/filtros.css`, saida versionada.
- O que vai ao ar fica em `site/`; o servidor local serve so `site/`, e `servir.py`, `dados/` e README
  respondem 404.
- Filtro: radios `sr` dentro de `.catalogo` + `label` estilizado de chip +
  `.catalogo:has(#cat-X:checked) .categoria:not([data-categoria="X"]) { display: none }`.
  Radio da acessibilidade de graca (setas trocam, leitor anuncia o marcado). Filtro `position: sticky`
  com fundo de papel; no celular, uma linha com `overflow-x: auto`.
- Placeholder de capa com `<symbol>` + `<use href="#boneco">`: estilo por atributo
  (`stroke="currentColor"`) no simbolo, cor pelo `color` do `<svg>` que usa.

### Ruido que nao e do subcaminho
`forge2d` pede `box2d.wasm` em `/packages/...` e `/<jogo>/packages/...` (dois `404`) antes de achar
`assets/packages/...`. Ja acontecia na raiz.

## Relacionado
- [[Rabisco-Hub]]
- [[hub-aponta-para-os-jogos-sem-iframe]]
- [[http-server-do-python-serve-build-velho]]
- [[18-security-headers]]
