---
tipo: projeto
titulo: Rabisco Games - hub dos jogos
projeto: [Rabisco-Hub, RagdollGames]
stack: [html, css, python]
tags: [tipo/projeto, stack/html, dominio/jogos]
palavras-chave: [hub, portal, vitrine, catalogo de jogos, pagina inicial, site do estudio, rabisco games, servir.py, gerar.py, jogos.json, porta 5120, base href, capa, selo, quadrado, filtro por categoria, RabiscoGames/hub]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-22
confianca: alta
---

# Rabisco Games - hub dos jogos

## Resumo
Pagina estatica que lista **os 84 jogos do catalogo** em quadrados com filtro por categoria e aponta
para cada um em `/<slug>/`; o jogo continua abrindo sozinho pelo proprio link.

## Contexto
Criado em 2026-09-12 a pedido do Jonas. No mesmo dia virou catalogo inteiro: "Eu irei criar todos
os jogos listados neste arquivo" (`ragdoll-games/catalogo-de-franquias.md`). Hoje lista a linha
[[RagdollGames]]; os Progressivos ([[Games]]) entram como categoria quando ele quiser.

## Detalhe

### Onde fica
`~/Documents/Repos/Games/hub` - repo privado **github.com/RabiscoGames/hub** (branch `main`).

| Caminho | Serve para |
| --- | --- |
| `dados/jogos.json` | **fonte unica**: 11 categorias e 84 jogos (slug, nome, categoria, estado, piada, pitch, nota, capa, alt) |
| `modelo.html` | casca da pagina com `$aviso $destaques $total $filtros $categorias` (`string.Template`) |
| `gerar.py` | valida o JSON e escreve `site/index.html` + `site/filtros.css` |
| `servir.py` | servidor local; os jogos permitidos sao os slugs do JSON |
| `site/` | **o que vai ao ar, e so isso** - `servir.py`, `dados/` e README ficam fora (404 conferido) |
| `site/estilo.css` | tokens da serie + layout |
| `site/img/` | `logo.png` (papel transparente), favicon, capas em `jogos/` |
| `site/_headers` | security headers para Cloudflare Pages / Netlify |
| `ferramentas/tinta_transparente.py` | arte a caneta de fundo branco -> PNG com alfa |
| `ferramentas/tracos_do_logo.py` | o `logo.png` virado em riscos de hachura para a abertura dos jogos ([[abertura-de-estudio-com-logo-riscado]]) |
| `site/img/logo-tracos.svg` | previa dos riscos, para conferir de olho |

O JSON **nao guarda a Ref. interna** do catalogo (nome da franquia original) nem o risco 🟢🟡🔴 -
so o que pode ir ao ar.

### Rodar
```bash
cd ~/Documents/Repos/Games/hub
python3 gerar.py              # depois de mexer no JSON ou no modelo
python3 gerar.py --conferir   # sai 1 se site/ esta desatualizado
python3 servir.py             # http://127.0.0.1:5120
```
Porta do hub **5120** (5125 Counter-Ragdoll, 5130 Ragdoll GO). Passo a passo para iniciar, clone
do zero em outra maquina e tabela de problemas comuns: secao **"Como iniciar"** no topo do
`README.md` (2026-09-13). O `servir.py` responde porta ocupada com mensagem e `lsof` sugerido
(sai 1), porta invalida com o uso (sai 2) e Ctrl+C com `Hub parado.` (sai 0) - sem traceback.
O hub nao precisa de `gerar.py` depois de clonar (saida gerada versionada) e build novo de jogo
aparece sem reiniciar o servidor; so jogo novo ou `slug` mudado no JSON pede reinicio.

### A pagina
- **Jogue agora**: cartao grande para `estado` `jogavel` e `obras` (hoje os tres `jogavel`:
  Counter-Ragdoll, Need for Ragdoll - desde 2026-09-22 - e Ragdoll GO).
- **Todos os jogos**: filtro fixo no topo (radio + `:has()`, sem JS) e uma secao por categoria com um
  quadrado por jogo. Sem capa, o quadrado mostra o boneco rabiscado (`<symbol id="boneco">`). `papel`
  tem contorno tracejado a lapis e sem link; os outros ganham selo e link.
- Celular: filtro numa linha que rola de lado, grade em 2 colunas.

### Jogo novo / jogo que avancou
So o `dados/jogos.json` + `python3 gerar.py`. O jogo ganha link quando `estado` sai de `papel`; a
pasta do repo do jogo tem de ter o mesmo nome do `slug`. Capa:
`sips -s format jpeg -s formatOptions 80 --resampleWidth 720 origem.png --out site/img/jogos/<slug>.jpg`
(o `gerar.py` le largura e altura sozinho). No JSON: `"capa": "<slug>.jpg"` + `"alt"` descrevendo a cena.

**O hub serve o build do clone em `~/Documents`**, nao do lugar onde o jogo foi feito: `servir.py`
le `../<slug>/build/web`. Se o trabalho rodou num clone fora do iCloud (`/tmp/...`), o `Jogar` abre
build velho ate o `build/web` de la ser trocado. Com o SDK do Flutter evictado, o build vem do
artefato `jogo-web` do CI (`gh run download <run> -n jogo-web`) - ver
[[2026-09-22-rabisco-hub-need-for-ragdoll-jogavel]].

`hub/hub/` (2026-09-22): clone aninhado do proprio hub, nao versionado, em `52e4a22`. Nao publica
nada (so `site/` vai ao ar); lixo a remover quando o Jonas confirmar.

**Quando o Jonas solta capas direto em `site/img/jogos/`** (2026-09-20): vieram como **PNG 1536x1024
com extensao `.jpg`** (~3,8 MB cada) e com nome que nao batia com o slug (`among-ragdoll.jpg` para o
slug `among-ragdolls`). Sempre conferir com `file` e contra o `slug` do JSON, converter pelo `sips`
acima e tirar o original de `site/` (vai ao ar inteiro). Capas hoje (8): counter-ragdoll, ragdoll-go,
need-for-ragdoll, grand-thief-ragdoll, doodlecraft, ragdoll-fc, angry-ragdolls, among-ragdolls.

### Nomes repetidos no catalogo
- **Super Ragdoll Bros** existe em Luta e esporte (slug `super-ragdoll-bros-luta`) e em Plataforma
  (`super-ragdoll-bros`) - o `gerar.py` avisa a cada execucao.
- **Ragdoll Royale** e jogo (Casual) **e** reserva do Ragdollnite; **The Elder Ragdolls** e jogo (RPG)
  **e** reserva do Ragdollrim. Se um reserva for acionado, colide.

### Seguranca
Estatico, sem cookie, sem analytics, sem fonte externa. [[18-security-headers]]: CSP
`default-src 'self'; script-src 'none'...` no `<meta>` e no `_headers`; `X-Frame-Options DENY`,
`Permissions-Policy` fechada, `nosniff`, HSTS. Os jogos **nao** herdam a CSP do hub. Antes do
primeiro push, auditoria de segredos com `grep` - so falso positivo ("tokens" de design).

## Relacionado
- [[RagdollGames]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[hub-aponta-para-os-jogos-sem-iframe]]
- [[hub-gerado-por-script-sem-js]]
- [[mix-blend-mode-multiply-nao-apagou-o-branco-do-logo]]
- [[2026-09-12-rabisco-hub-de-jogos]]
- [[2026-09-12-rabisco-hub-catalogo-e-git]]
- [[2026-09-20-rabisco-hub-capas-novas]]
- [[2026-09-22-rabisco-hub-need-for-ragdoll-jogavel]]
- [[preview-start-nao-le-documents]]
- [[Sites-Estaticos]]
