---
tipo: armadilha
titulo: mix-blend-mode multiply nao apagou o branco do logo sobre a pauta
projeto: [Rabisco-Hub, RagdollGames]
stack: [css, html, python]
tags: [tipo/armadilha, stack/css]
palavras-chave: [mix-blend-mode, multiply, fundo branco, logo, png transparente, alfa, luminancia, pauta, background, canvas, filter, feDisplacementMap, sips, PIL, imagemagick, webp]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# mix-blend-mode multiply nao apagou o branco do logo

## Resumo
Logo de fundo branco com `mix-blend-mode: multiply` (e `filter: url(#tremor)` animado) sobre a pauta
desenhada no fundo do `body` virou um **retangulo liso por cima das linhas** no Chromium do painel.
PNG com alfa resolveu na hora.

## Contexto
[[Rabisco-Hub]], 2026-09-12. Duas rodadas gastas tentando fazer o blend funcionar.

## Detalhe

### O que nao resolveu
- Mover o papel para o `html` e deixar a pauta so no `body` (suspeita: fundo do `body` propagado
  para o canvas nao entra no blend). Nao bastou.
- Nao isolei se foi `filter` + `mix-blend-mode` no mesmo elemento. Causa **nao confirmada**.

Dentro do cartao - que tem `transform`, portanto contexto de empilhamento com fundo proprio - o
mesmo `multiply` nas capas funcionou.

### O que resolveu
Arte monocromatica (tinta sobre papel) vira **PNG com alfa pela luminancia**, tinta numa cor so.
A maquina nao tem PIL, ImageMagick nem cwebp, e `sips` nao escreve webp. Python puro com `zlib`,
1,4 s para 960x706:
```bash
sips --resampleWidth 960 original.png --out /tmp/logo-960.png
python3 ~/Documents/Repos/Games/hub/ferramentas/tinta_transparente.py /tmp/logo-960.png img/logo.png
```
Entrada PNG RGB/RGBA 8 bits sem entrelacamento. Constantes no topo: `TINTA` (cor final),
`PAPEL_LUM 242` (acima some), `TINTA_LUM 70` (abaixo fica opaco).

Recorte com `sips`: `sips -c <altura> <largura> --cropOffset <y> <x> in.png --out out.png`.

### A licao
Se a arte e tinta sobre papel, o arquivo certo e **transparente** - nao gastar rodada fazendo blend
funcionar por cima de fundo de pagina. Blend so quando o fundo esta dentro de uma caixa propria.

## Relacionado
- [[Rabisco-Hub]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[testar-html-local-sem-servidor-engana]]
