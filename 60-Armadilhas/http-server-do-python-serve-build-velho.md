---
tipo: armadilha
titulo: python -m http.server serve build velho e mascara toda correcao
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/armadilha, stack/flutter, dominio/jogos]
palavras-chave: [cache, http.server, python, flutter web, build, verificacao, no-store, cache-control, depuracao]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: alta
---

# python -m http.server serve build velho
## Resumo
`python3 -m http.server` nao manda `Cache-Control`, entao o navegador guarda `main.dart.js` por
heuristica e continua rodando o **build anterior** por mais que se recompile.

## Contexto
Custou uma sessao inteira no `counter-ragdoll` (2026-09-10). O mouse nao girava a camera; foram
seis rodadas de correcao e diagnostico - trocar import condicional, trocar `Listener` por
`MouseRegion`, escrever ponteiro travado, instrumentar contadores - e **nenhuma delas estava
rodando**. Ao servir o mesmo build numa porta nova, funcionou de primeira.

## Detalhe

### Por que engana tanto
- `?v=N` na URL busta so o **HTML**; `main.dart.js` e pedido sem query e vem do cache.
- Nao ha service worker envolvido (`getRegistrations()` = 0), entao a pista obvia nao aparece.
- Nao ha erro no console: o codigo velho roda perfeitamente, so nao tem a correcao.
- Recompilar mostra `✓ Built build/web`, o que da falsa confianca.

### Como servir
```bash
python3 -c "
import http.server, socketserver
class SemCache(http.server.SimpleHTTPRequestHandler):
    def end_headers(self):
        self.send_header('Cache-Control','no-store, must-revalidate')
        super().end_headers()
socketserver.TCPServer.allow_reuse_address = True
with socketserver.TCPServer(('',5125), SemCache) as s: s.serve_forever()
"
```

### O sinal que denuncia
**Uma mudanca que nao pode falhar nao aparece.** Se um texto novo na tela, um contador ou um
atributo no DOM simplesmente nao existe, a hipotese nao e "meu codigo esta errado" - e
**"este nao e o meu codigo"**. Confirmar com `grep` no bundle:
```bash
grep -c "texto novo" build/web/main.dart.js
```
Se o texto **esta** no bundle e **nao** aparece na tela, e cache.

### Atencao ao grep
`grep -c "pointerLock"` nao casa `requestPointerLock` - maiuscula. E texto com acento vira
escape (`á`) no bundle do dart2js, entao `Armazém` nunca casa. Buscar trecho ASCII e
respeitar caixa.

## Relacionado
- [[verificar-jogo-no-navegador-do-painel]]
- [[raycasting-em-flutter]]
