---
tipo: sessao
titulo: Need for Ragdoll - o escopo vira "o Underground inteiro" e o CI fica verde
projeto: [RagdollGames, Rabisco-Hub]
stack: [flutter, dart, github-actions]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll, area/ci]
palavras-chave: [need for speed underground, escopo, fidelidade, menus sem mundo aberto, PR 11, PR 12, PR 13, fonte empacotada, discarded_futures, unawaited, osv, advanced security, ci verde, limite de uso, resumeFromRunId]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-23
confianca: alta
---

# O escopo vira "o Underground inteiro"

## Resumo
Jonas decidiu **fidelidade acima de custo**: o jogo tem de ser o NFS Underground, so que em
rabisco. Sem mundo aberto (navegacao por menu), mas com todas as mecanicas, modos e angulos de
camera dentro das fases. E o CI, que e o unico lugar que compila, ficou verde.

## Contexto
Depois de abrir o jogo com o texto funcionando e achar que parecia um ferrorama. As duas frases
dele estao citadas em [[cara-de-need-for-speed-underground]], que virou o documento da decisao.

## Detalhe

### O que a decisao mudou no vault
- [[need-for-ragdoll-visto-de-cima-sem-gravidade]] marcada como **REVOGADA pelo dono** - ela
  tinha sido tomada por custo, e o criterio mudou. Continua valendo como inventario do que a
  mudanca custa (gravidade zero, arrastarNoChao, angulo inicial do ragdoll, metrosPorPixel).
- O escopo oficial: **menu como no Underground 1**, fidelidade total dentro das fases, garagem
  e atividades. Rabisco e a unica licenca artistica.

### Tres PRs, todos verdes no que importa
| PR | O que e | CI |
| --- | --- | --- |
| #11 | Fonte empacotada: Roboto do cache do SDK em `assets/fontes`, `family: Roboto` no pubspec, teste em `test/lancamento` e passo no job `build` que confere o `FontManifest.json` do artefato | `testes`, `fisica`, `build` verdes - **o passo da fonte passou no CI**, ou seja a maquina que compila confirmou |
| #12 | `unawaited(...)` nas quatro chamadas de `Ticker.start()` | **`analise` passou** - era o vermelho que vinha do PR #7 |
| #13 | `upload-sarif: false` no scan de dependencias | ver [[osv-scanner-reusable-exige-advanced-security]] |

### O inventario que o estudo ja devolveu (4 de 12 agentes antes do limite)
Dos ~38 itens que compoem uma fase do Underground, **o repo cobre 7 de forma reconhecivel**.
Nao existe no codigo: marcha, tacometro, temperatura de motor, transito, policia, dano,
pontuacao de drift, multiplicador, knockout, drag, sprint, replay, velocimetro, minimapa nem
troca de camera. O item que domina o custo **nao e nenhum modo: e a camera** - no Underground
ela fica atras do carro, gira com ele e o carro ocupa quase um terco da tela.

### Aprendizados de processo
- **Limite de uso da conta matou juiz e ceticos em tres workflows seguidos.** `resumeFromRunId`
  recupera: os agentes concluidos voltam do cache e so os que faltaram rodam. O `scriptPath`
  precisa ser legivel do diretorio atual - se o workflow nasceu com outro cwd, copie o script
  para o scratchpad antes de retomar.
- [[replace-sem-assert-mente-e-o-agente-relata-sucesso]] - custou um relato errado ao Jonas.
- Disco: 4,3 GB livres de 228 GB. Os caches recuperaveis medidos: Spotify 1,2 G, Google 1,2 G,
  VSCode ShipIt 901 M, JetBrains 839 M (~4,1 G); simuladores do Xcode guardam 4,6 G em
  aparelhos. Abaixo de ~10 GB livres o Dart trava -
  [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]].

### O lote de dominio comecou (PR #14, branch `underground-dominio`)
Escrito **a mao, nao por agentes** - ver a licao abaixo. Cada peca commitada
sozinha e verificada pelo CI:

| Pacote | O que e | CI |
| --- | --- | --- |
| Drift pontuado | os tres ingredientes que o repo ja media (velocidade, `derrapagemDe`, duracao) somados; multiplicador por degrau; o drift so fecha depois de uma folga com o carro reto (senao nao existe drift em serie); bater perde o drift **em curso**, nao o placar | 19 testes verdes |
| Comando do drag | sem volante: direcao por faixa, troca de marcha como pedido de um quadro so | verde |
| Motor do drag | giro que sai da velocidade e da marcha, faixa azul e verde, ganho da troca perfeita, castigo da ruim, temperatura que estoura o motor no meio da prova | 23 testes verdes |
| Tuning | as 9 pecas em 4 degraus sobre o `Chassi` (que ganhou `copyWith`); pneu melhor **nao** mata o drift; nitro fica fora do chassi; barras e estrelas | 17 testes verdes |
| **Camera** | `Projetor` (perspectiva pinhole com altura), `RigDeCamera` (os 5 angulos do Underground) e `PontoNoMundo` (o mundo ganhou `z`) | 20 testes verdes |

### Tres achados da lente (a peca que sustenta a terceira pessoa)
1. **A vista de cima de hoje e a mesma lente com inclinacao `pi/2`.** Nao foi
   planejado, caiu da conta: com o rumo travado em `-pi/2`, o projetor devolve
   o enquadramento atual (carro no centro, `+y` do mundo para baixo). Por isso
   da para **descer** da folha para tras do carro sem trocar de motor - a
   largada vira o plano de abertura da corrida.
2. **A inclinacao nao e numero ajustado a mao: e `atan2(altura, recuo +
   avanco)`.** A folha cai em `pi/2` sozinha porque recuo e avanco sao zero.
3. **O `avanco` existe para o carro NAO ficar no centro exato.** Camera atras
   de carro centralizado vira prato giratorio - carro parado no meio, mundo
   rodando embaixo. Foi o cetico que pegou isso.

E a correcao do cetico que entrou no enum desde o primeiro dia: a **camera do
voo** fica fora do rodizio do jogador, porque com a camera atras do carro o
motorista voa para LONGE de quem joga - a piada da serie aconteceria fora de
quadro.

### A licao do lote: agente frio custa caro num repo lento
Dois workflows de implementacao (5 + 6 agentes) morreram **inteiros** no limite
de uso e produziram **um arquivo**. Motivo: cada agente comeca frio e gasta o
orcamento relendo o repo, e aqui cada comando de git leva de 1 a 4 minutos
(iCloud). Escrevendo eu mesmo, com o contexto ja carregado, sairam quatro
pacotes commitados e verificados no mesmo tempo.
**Regra:** workflow para pesquisa e revisao adversarial (onde ja pegou dois
erros graves meus hoje); implementacao em repo lento, a mao.

### O que o CI pega e eu nao vejo
**PR #14 fechou verde nos quatro jobs** (analise, testes, fisica, build) com os
quatro pacotes e 59 testes novos. Foram tres rodadas ate la, e cada erro so
existia porque aqui nao ha compilador. Sem ele, estes so aparecem no CI:
- `prefer_int_literals` reclama do `0.0` escrito justamente para fugir do `num`
  que `math.max(0, x)` devolveria. Saida: nao usar `math.max` - ternario.
- import que ficou morto depois de tirar o `math.`.
- `specify_nonobvious_property_types` num `const passo = 1 / 60` de teste.
- `prefer_const_literals_to_create_immutables` em conjunto passado para classe
  `@immutable`.
- `comment_references`: `[membro]` no doc de um enum nao enxerga membro de outra
  classe - escreva `[Classe.membro]`.
- `avoid_renaming_method_parameters`: `operator ==` tem de receber `other`,
  mesmo num repo escrito em portugues.
- `use_setters_to_change_properties` pede setter no lugar do metodo; ai
  `unnecessary_getters_setters` pede o **campo publico** no lugar dos dois. O
  destino de `void irPara(x)` era `AnguloDeCamera angulo;` o tempo todo.
- `avoid_redundant_argument_values`: passar o valor igual ao padrao e ruido.
- **Erro de compilacao derruba os QUATRO jobs juntos** - quando os quatro caem,
  nem leia o lint: e o codigo que nao compila. Foi
  [[campo-privado-no-construtor-vira-parametro-publico]].

**A regra que sai disto:** antes de inventar como uma API se chama, olhe como o
codigo vizinho ja a chama. A `bitola` e o `other` estavam os dois a vista.

### Pendente
**O proximo passo e o Marco 1**: "a mesma corrida do recreio, vista de tras" -
chao e ceu desenhados pela lente (a folha vira a TELA, pauta reta e fixa, mundo
em perspectiva por cima), pista como duas polilinhas que se estreitam ate o
horizonte, e o carro deixando de ser retangulo visto de cima. Nenhuma regra de
corrida muda.

Tambem pendente: o estudo do viaduto (a resposta "quero viaduto" reabriu a
pergunta de 3D e ele nunca rodou); mergear #11, #12, #13 e #14; commit do hub;
liberar disco (4,3 GB livres impedem qualquer um de abrir o jogo e olhar).

## Relacionado
- [[cara-de-need-for-speed-underground]]
- [[RagdollGames]]
- [[2026-09-22-rabisco-hub-need-for-ragdoll-jogavel]]
- [[csp-self-apaga-todo-o-texto-do-flutter-web]]
- [[campo-privado-no-construtor-vira-parametro-publico]]
- [[fable-planeja-claude-code-implementa]]
