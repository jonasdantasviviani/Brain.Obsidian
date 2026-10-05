---
tipo: sessao
titulo: Rabisco Games - hub com todos os jogos
projeto: [RagdollGames, Rabisco-Hub]
stack: [html, css, python]
tags: [tipo/sessao, stack/html, dominio/jogos]
palavras-chave: [hub, portal, vitrine, catalogo, pagina inicial, rabisco games, logo, capa, base href, servir.py, flutter web, subcaminho, mix-blend-mode, png transparente, porta 5120]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Rabisco Games - hub com todos os jogos

## Resumo
Jonas: "quero fazer um hub dos jogos do Rabisco games ... mas tambem vou conseguir acessar os jogos
por fora". Nasceu `Repos/Games/hub`: pagina estatica que so aponta; cada jogo segue site proprio em
`/<jogo>/`.

## Contexto
Mandou o logo Rabisco Games (`~/Downloads/Logo Rabisco Games.png`, 1254 px, fundo branco) e, no meio
da sessao, as capas dos cinco jogos (`~/Downloads/<Nome do jogo>.png`).

## Detalhe
- HTML + CSS puros, **zero JavaScript**, CSP `script-src 'none'` no `<meta>` e no `_headers`.
- Cinco cartoes com a capa: Counter-Ragdoll e Ragdoll GO "Prototipo jogavel", Need for Ragdoll
  "Em construcao" (botao "Espiar"), Doodlecraft e Grand Thief Ragdoll "No papel" (sem link).
- Texto dos cartoes fala so do genero - nenhuma franquia citada.
- `servir.py` na porta **5120** serve hub + `../<jogo>/build/web` em `/<jogo>/`, reescrevendo o
  `<base href>` na hora. Ver [[hub-de-jogos-web-um-caminho-por-jogo]].
- Logo virou `img/logo.png` com o papel transparente, gerado por `ferramentas/tinta_transparente.py`.

### Verificacao
- `curl`: headers do hub, `301` de `/ragdoll-go` para `/ragdoll-go/`, `<base href="/<jogo>/">` nos
  tres builds, `main.dart.js` `text/javascript`, `canvaskit.wasm` `application/wasm`, `404` explicando
  em `/doodlecraft/`, quatro tentativas de path traversal com `--path-as-is` todas `404`.
- Navegador do painel: Counter-Ragdoll (menu) e Ragdoll GO (folha com bicho) abrem por `/<jogo>/`;
  logo sobre a pauta; capas inteiras; 375 px sem rolagem horizontal; tremor ativo
  (`animationName: tremor`, `filter` trocando de semente).

### Tropecos
- `mix-blend-mode: multiply` no logo de fundo branco virou retangulo por cima da pauta - ver
  [[mix-blend-mode-multiply-nao-apagou-o-branco-do-logo]].
- Capa cortada em vez de inteira: `height: 100%` numa `<img>` item de grid dentro de caixa com
  `aspect-ratio` nao resolve. `position: absolute; inset: 0` resolveu.
- Os dois `404` de `box2d.wasm` no console sao o `forge2d` testando caminhos - ja existiam.
- Primeira versao dos cartoes tinha desenhos SVG feitos a mao; as capas do Jonas chegaram e
  substituiram.

## Pendencias
- Hospedagem e dominio (ver [[hub-aponta-para-os-jogos-sem-iframe]]).
- Capas com conflito com `nomes-e-marcas.md` - lista em [[RagdollGames]] ("Capas dos jogos").
- ~~Nada versionado~~ - versionado em `RabiscoGames/hub` na sessao seguinte, ver
  [[2026-09-12-rabisco-hub-catalogo-e-git]].
- Fonte manuscrita propria com subset (hoje pilha do sistema).

## Relacionado
- [[Rabisco-Hub]]
- [[RagdollGames]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[hub-aponta-para-os-jogos-sem-iframe]]
- [[mix-blend-mode-multiply-nao-apagou-o-branco-do-logo]]
- [[18-security-headers]]
