---
tipo: decisao
titulo: Hub aponta para os jogos, sem iframe, um caminho por jogo
projeto: [Rabisco-Hub, RagdollGames]
stack: [html, flutter]
tags: [tipo/decisao, dominio/jogos]
palavras-chave: [hub, iframe, embutir jogo, link direto, subcaminho, subdominio, hospedagem, github pages, cloudflare pages, netlify, dominio, vitrine]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-23
confianca: alta
---

# Hub aponta para os jogos, sem iframe

## Resumo
O hub e so vitrine: "Jogar" navega para `/<jogo>/` na pagina inteira. Nenhum jogo roda dentro do hub.

## Contexto
Jonas pediu o hub para "ver os jogos", com a condicao de "tambem acessar os jogos por fora".
Decidido por mim na implementacao (2026-09-12); a hospedagem ficou para ele.

## Detalhe

### Decisao
- Um dominio, um caminho por jogo: `<dominio>/` hub, `<dominio>/<jogo>/` jogo.
- Jogo compilado com `--base-href /<jogo>/`; o link dele funciona sem o hub.
- Hub sem build e sem JavaScript.

### Alternativas
| Opcao | Situacao | Por que |
| --- | --- | --- |
| iframe do jogo dentro do hub | **descartada** | teclado, ponteiro travado (Counter-Ragdoll), tela cheia, audio e camera (Ragdoll GO) sofrem dentro de iframe; obrigaria afrouxar `frame-ancestors`/`X-Frame-Options`; o link direto viraria segunda classe |
| subdominio por jogo (`counter-ragdoll.<dominio>`) | **em aberto** | casa com um projeto de hospedagem por repo, mas pede DNS por jogo. Troca so o `href` dos cartoes |

### Hospedagem - DECIDIDA em 2026-09-23
Ver [[onde-a-rabisco-vive]]: dominio `rabiscostudio.com.br` no Registro.br, hub e jogos no
**Cloudflare Pages de graca**, os jogos num **subdominio proprio** (`games.`) porque origem e a
unica coisa irreversivel - e sistema de cliente **nunca** no dominio da Rabisco, porque a
fronteira de cookie e o dominio registravel, nao a origem. A tabela abaixo fica como historico.

### Hospedagem - a comparacao de 2026-09-12 (historico)
| Onde | A favor | Contra |
| --- | --- | --- |
| GitHub Pages | org site na raiz + cada repo vira `/<repo>/` sozinho - o formato exato do hub | repo **privado** exige plano pago na organizacao; nao aceita headers (so a CSP do `<meta>`, sem `frame-ancestors`) |
| Cloudflare Pages | repo privado de graca, le o `_headers` | um projeto por repo cai em subdominio; caminho unico pede juntar os builds num deploy so |
| Netlify | igual ao Cloudflare Pages, le `_headers` | limites menores no gratis |

### Quando rever
Se entrar placar online ou conta: o hub passa a ter back-end e valem [[22-cors-restritivo]] e
[[14-validacao-dos-inputs]].

## Relacionado
- [[Rabisco-Hub]]
- [[onde-a-rabisco-vive]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[um-repo-por-jogo-com-docs-da-linha]]
- [[tecnologia-mais-chata-que-resolve]]
