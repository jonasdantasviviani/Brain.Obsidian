---
tipo: decisao
titulo: Onde a Rabisco vive - dominio, hospedagem e o dia da sala
projeto: [Rabisco-Hub, RagdollGames, Cerebro]
stack: [cloudflare, dns, seguranca, dart]
tags: [tipo/decisao, area/infra, dominio/jogos]
palavras-chave: [rabiscostudio, dominio, hospedagem, cloudflare pages, coolify, vps, hetzner, hostgator, registro.br, subdominio, origem, cookie, etld+1, csp, hsts, websocket, sala, multijogador, latencia sao paulo, lgpd, parodia]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-23
confianca: alta
---

# Onde a Rabisco vive

> Fecha a tabela de hospedagem que estava **em aberto** desde 2026-09-12 em
> [[hub-aponta-para-os-jogos-sem-iframe]]. Levantado por 7 agentes; os dois
> ceticos nao derrubaram o desenho, mas corrigiram varias contas - o que esta
> aqui ja e a versao corrigida.

## Resumo
Compre **so o dominio**. Hospede no **Cloudflare Pages de graca**, com os jogos
num **subdominio proprio**. Nada de VPS, Coolify, Hetzner ou hospedagem
compartilhada **hoje**. E a regra que nao se desfaz: **sistema de cliente nunca
no dominio da Rabisco**.

## Contexto
Jonas: "eu quero comprar e comecar a publicar meus jogos... meu dominio vai ser
rabisco studio, onde terei tambem a rabisco games. O studio vou criar sites e
sistemas, o games postar meus jogos". Mais: quer **jogo em rede com sala** depois.

**Eu tinha recomendado VPS + Coolify antes deste estudo.** Estava errado para o
momento: aquilo resolve o dia da sala, nao o dia de hoje, e cobra operacao desde
ja. O Coolify continua certo - mas depois, e so para o servidor de sala.

## Detalhe

### O desenho
| Nome | O que e | Onde |
| --- | --- | --- |
| `rabiscostudio.com.br` | Rabisco Studio - vitrine, **sem login e sem cookie** | Cloudflare Pages (projeto 1) |
| `games.rabiscostudio.com.br/` | o hub dos jogos | Cloudflare Pages (projeto 2) |
| `games.rabiscostudio.com.br/<slug>/` | cada jogo, um caminho por jogo | mesmo projeto 2 |
| `games.rabiscostudio.com.br/sala` | servidor de sala, **quando existir** | VPS em Sao Paulo |
| sistema de cliente | **fora do dominio da Rabisco** | dominio do proprio cliente |

A decisao de 2026-09-12 (um dominio, um caminho por jogo, `--base-href
/<jogo>/`) continua intacta: muda so **qual host e a raiz**. `gerar.py` e
`servir.py` nao mudam uma linha.

### Por que subdominio, e nao caminho
A **origem** (esquema + host + porta) e a unica coisa irreversivel aqui.
`rabiscostudio.com.br/cliente-x/` e `.../counter-ragdoll/` sao a **mesma
origem**: dividem `localStorage`, DOM e service worker. Um XSS em qualquer um
dos 84 jogos leria o armazenamento do sistema do cliente.

E ha um motivo tecnico que nao e opiniao: a CSP dos jogos e **obrigatoriamente
frouxa** (CanvasKit exige `'wasm-unsafe-eval'` e `style-src 'unsafe-inline'`),
enquanto a do hub pode ser `script-src 'none'`. As duas nao cabem na mesma
origem sem uma delas perder.

Custo de fazer certo hoje: **um registro CNAME**. Custo de consertar depois:
migrar de origem **apaga o `localStorage` de todo jogador** - save e recorde -,
quebra todo link divulgado e exige 301 para sempre.

### A correcao que o cetico trouxe: cookie NAO obedece origem
Cookie segue o **dominio registravel** (eTLD+1 = `rabiscostudio.com.br`, porque
`com.br` esta na Public Suffix List), nao a origem. Ou seja: subdominio isola
`localStorage`, IndexedDB, service worker e DOM - **mas nao isola cookie**.

> **A regra, literal:** nada que tenha sessao, cookie ou login mora no dominio
> registravel `rabiscostudio.com.br` - **nem em subdominio**. A fronteira de
> cookie e o dominio registravel, nao a origem.

Por isso "sistema de cliente vai no dominio do cliente" nao e preferencia: e a
unica fronteira que o navegador respeita de verdade.

### A lista de compras de hoje
- **Dominio** `rabiscostudio.com.br` no Registro.br - **R$ 40,00/ano**. O
  Registro.br cobra o mesmo no registro e na renovacao (sem a pegadinha de
  renovacao dobrada das revendas). Da para pagar **ate 5 anos** de uma vez -
  conferir o desconto no checkout.
- **Hospedagem: R$ 0,00.** Cloudflare Pages gratis, dois projetos, aceita repo
  privado e le o `site/_headers` que ja existe.
- **TLS e redirect HTTP->HTTPS: R$ 0,00**, automatico.

### O que a conta tinha esquecido (correcoes do cetico do bolso)
1. **GitHub Actions.** A org `RabiscoGames` esta no plano free: 2.000 min/mes.
   No ritmo atual de rodadas isso estoura, e o excedente e US$ 0,006/min - ate
   ~R$ 100/mes num mes intenso. E custo de **hoje**, nao do cenario com servidor.
2. **`git push` nao publica.** Nenhum repo contem o artefato: `build/` esta no
   `.gitignore` dos tres jogos, e o `site/` do hub nao tem os jogos dentro.
   Falta montar um release entre repositorios. Isso e **2 a 4 h no primeiro
   mes** e ~1 h/mes depois - nao "zero operacao".
3. **E-mail.** Estudio que atende cliente com Gmail pessoal no rodape perde
   trabalho, e a tabela nao tinha essa linha. Zoho Mail gratis cobre (1 dominio,
   ate 5 contas, so webmail/app).
4. No cenario com VPS faltavam **snapshot** (~20% da instancia) e **monitoracao**
   (UptimeRobot gratis resolve).

### O que NAO fazer
- **Jogo publico e sistema de cliente na mesma origem.** O erro caro e
  irreversivel deste estudo.
- **Decidir a origem depois do primeiro jogo ir ao ar.** Migrar apaga save e
  recorde de todo jogador - o navegador nao migra `localStorage` entre origens.
- **Submeter o dominio em hstspreload.org.** O header HSTS expira; o preload
  entra no binario do navegador, vale para todo subdominio futuro, e sair leva
  meses. E, no apice, publique `max-age` **sem** `includeSubDomains`, a menos
  que voce aceite que todo subdominio futuro nasca HTTPS-only.
- **Comprar VPS ou Hetzner hoje.** Hetzner nao tem datacenter na America do Sul:
  Falkenstein fica a ~200 ms de Sao Paulo, e acima de 150 ms jogo de tiro e
  injogavel.
- **GitHub Pages**: ignora o `_headers` e o teto de 1 GB morre com ~25 jogos
  (cada build ocupa ~40 MiB em disco).
- **Hospedagem compartilhada** achando que resolve o futuro: serve o estatico
  com `.htaccess`, mas nao faz WebSocket nem processo vivo.
- **Copiar o CanvasKit 84 vezes**: sao 36,6 MiB por build, byte a byte iguais
  entre os jogos. Ingenuamente da 3,31 GiB publicados; compartilhado, 0,34 GiB.
- **Publicar os `*.symbols`**: 8,2 MiB por jogo que o navegador nunca baixa.

### Detalhes do `_headers` que so aparecem na pratica
- O Cloudflare Pages le o `_headers` **so na raiz** da saida - nao ha aninhado.
  Com hub e jogos num projeto so, e **um arquivo** com regras por caminho.
- Limite de **100 regras**. Nao repita regra por jogo: use marcador de caminho
  (`/:jogo/flutter_service_worker.js`), tres regras para o catalogo inteiro em
  vez de 252.
- Regras que casam sao **cumulativas**, e header repetido e juntado por virgula:
  nao existe sobrescrever.

### O dia da sala
- Fica na **rede local** primeiro, como ja decidido em [[lobby-antes-da-rede]]:
  custo R$ 0,00 e prova o encanamento inteiro.
- So quando precisar sair para a internet: **VPS de 1 vCPU em Sao Paulo**, so o
  servidor Dart. O Cloudflare continua servindo os jogos. +R$ 30-60/mes e +2-4
  h/mes de operacao (estimativa).
- **A CSP vai barrar o WebSocket** se ninguem cuidar: `connect-src 'self'` nao
  resolve para `wss://` em todo navegador - tem de listar o esquema. E a mesma
  armadilha que apagou o texto do jogo em 22/09
  ([[csp-self-apaga-todo-o-texto-do-flutter-web]]), e o sintoma vai ser igual de
  silencioso: "Entrar na sala" nao faz nada e so o console conta.
- **Mesma origem nao e defesa para WebSocket.** O navegador nao aplica CORS a
  `wss://`, e uma allowlist de `Origin` com um valor so nao protege de nada,
  porque qualquer um dos 84 jogos - e qualquer XSS neles - tem essa origem. A
  defesa e auth, validacao e limite de taxa, na mao.

### O que o dono ainda precisa responder
1. `rabiscostudio.com.br` esta livre? (registro.br/busca-dominio; e conferir
   marca no INPI antes de pagar)
2. Vai hospedar sistema de cliente algum dia, ou o trabalho do estudio sempre
   entrega no dominio do cliente?
3. Os jogos vao ter placar online ou conta de jogador? Se **nao** (codigo de
   sala de 4 letras + apelido), a LGPD inteira sai da conta - inclusive o
   art. 14, que exige consentimento de responsavel para menor de 12 anos.
4. Multijogador continua "rede local por enquanto"?
5. Os repositorios ficam privados? (o Cloudflare aceita privado de graca)

## Relacionado
- [[hub-aponta-para-os-jogos-sem-iframe]]
- [[lobby-antes-da-rede]]
- [[csp-self-apaga-todo-o-texto-do-flutter-web]]
- [[18-security-headers]]
- [[19-forcar-https]]
- [[tecnologia-mais-chata-que-resolve]]
- [[Rabisco-Hub]]
