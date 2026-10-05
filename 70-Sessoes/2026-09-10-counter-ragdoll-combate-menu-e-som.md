---
tipo: sessao
titulo: Counter-Ragdoll - combate com efeito, menu do 1.6, opcoes, dificuldade e som
projeto: [RagdollGames]
stack: [flutter, dart, flame, web]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/fps]
palavras-chave: [combate, bot, mira, random, menu, cs 1.6, opcoes, modo dark, paleta, dificuldade, som, web audio, roadmap]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: alta
---

# Counter-Ragdoll - combate, menu e som
## Resumo
Do "nao tem efeito de tiro e minha vida so diminui" ate um jogo com menu no estilo do CS 1.6,
opcoes salvas, modo dark, quatro dificuldades e som sintetizado - e o CS 1.6 inteiro mapeado.

## Contexto
Continuacao de [[2026-09-10-counter-ragdoll-morte-mira-e-arma]]. Jogo em
`~/Documents/Repos/Games/counter-ragdoll`, servido em `localhost:5125` (sem cache).

## Detalhe

### 1. Combate que se ve
Relato do Jonas: sem efeito de tiro nem de facada, bots parados, so a vida caindo.
**Simulacao de 20 s no host antes de mexer** mostrou a causa na primeira leitura:
- bot parava a 6 celulas e **nunca errava** - `Random(bot.hashCode)` recriado a cada tiro
  ([[random-com-semente-fixa-a-cada-sorteio]])
- depois de corrigido, a mesma simulacao pegou o bot **travado 12 s numa quina**: `_andar`
  contava "andou" por um fio de movimento; agora mede quanto andou e contorna
- efeitos: clarao no bot ao atirar, borda vermelha + seta de onde veio o dano, X na mira ao
  acertar, mancha de tinta onde a bala bate, golpe da faca, susto do bot ao levar tiro

### 2. Menu, opcoes, dificuldade e som (pedido "fiel ao CS 1.6, em rabisco")
- menu de lista de texto a esquerda, marca-texto no item sob o mouse; Novo jogo -> fase ->
  dificuldade; Opcoes; Como jogar; Continuar no ESC com a partida pausada **por baixo**
- 13 opcoes salvas com `shared_preferences`, **validadas ao ler** (armazenamento local e
  editavel pelo usuario)
- modo dark como paleta trocavel ([[widget-const-nao-ve-troca-de-paleta]])
- quatro dificuldades: reacao, mira, desvio e susto
- som por Web Audio, sem arquivo ([[som-sintetizado-com-web-audio]]), com passos e tiros dos
  bots espaciais
- `ROADMAP.md`: o CS 1.6 em 12 fases (rodadas, economia, loja, bomba, granadas, agachar...).
  A pagina do fandom que o Jonas mandou respondeu 402; o mapa veio do conhecimento do jogo.

### Verificado e nao verificado
- **Na tela**: menu claro e escuro, opcoes, fases, dificuldades, partida no escuro com o
  clarao do bot, e o modo dark **sobrevivendo a recarregar a pagina**
- **Por teste de widget** (sabotado para provar que pega o defeito): trocar o modo dark na
  propria tela redesenha o titulo, e a opcao fica salva
- **Nao verificado**: os sons - o painel nao deixa ouvir. Marcados como parciais no roadmap.

### Limites do painel de teste, de novo
Um clique por carregamento de pagina; a aba some e precisa de `preview_start` para voltar.
Efeito de menos de meio segundo (clarao, aviso de dano) nao se captura olhando - so por
teste de dominio ou por sorte. Ver [[verificar-jogo-no-navegador-do-painel]].

### 3. Captura do mouse
Jonas: o cursor chegava na borda da tela e travava a camera. Voltou a trava de ponteiro, agora
sem quebrar o gatilho: mouse todo cru por eventos de ponteiro, primeiro clique so captura, ESC
solta e abre o menu. O teste no painel pegou **dois defeitos antes de chegarem a ele**:
- o `pointerlockerror` parecia nao disparar - na verdade o pedido de trava nem era feito,
  porque o `mousedown` nunca chegava. Depois de corrigido, o erro veio numa execucao e nao em
  outra. A regra "uma tentativa e depois atira" entrou no commit `9f1bb90` (em 2026-09-11 eu
  afirmei o contrario de memoria e errei - [[commit-que-descreve-o-que-nao-foi-feito]])
- `mousedown` nunca chegava, porque o Flutter cancela o `pointerdown`
  ([[flutter-web-cancela-pointerdown]]) - o jogo **nao atiraria no Chrome dele**

### Resultado
Commits `ba90be0` (combate), `6799c2b` (menu, opcoes, dark, dificuldade, som) e o do teste de
widget. flutter analyze limpo, **93 testes**.

## Relacionado
- [[RagdollGames]]
- [[random-com-semente-fixa-a-cada-sorteio]]
- [[widget-const-nao-ve-troca-de-paleta]]
- [[som-sintetizado-com-web-audio]]
- [[verificar-jogo-no-navegador-do-painel]]
