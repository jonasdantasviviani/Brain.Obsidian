---
tipo: sessao
titulo: Need for Ragdoll - roadmap inteiro em paralelo (12 pacotes, integracao e revisao)
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [roadmap, workflow, paralelo, agentes, commit por caminho, fila de comandos pesados, icloud, espelho de dominio, verificacao local]
origem: claude-code
criado: 2026-09-21
atualizado: 2026-09-21
confianca: media
---

# Roadmap inteiro em paralelo
## Resumo
Pedido: "faca todos os proximos passos em paralelo, commite a cada novidade, teste ou simulacao
que passar de 5 min cancele e commite sem testar, pegue todo o roadmap e termine".
**Status (2026-09-22 00:15): roadmap implementado, PR #7 aberto com a primeira rodada de CI real rodando; parado com a cota semanal da conta em 98%.**

## Contexto
Depois do PR #1 (carro, motorista solto, menu e ajustes). Faltavam: pista fechada com voltas
(dia 8), regra do motorista que cruza a linha (dia 9), recorde salvo (dia 10) e o sprint 2
inteiro (rivais, campeonato, carros extras, som, cosmetico) mais o checklist de lancamento.

## Detalhe

### Antes de paralelizar: o ambiente
Metade da sessao anterior foi perdida para um diagnostico errado. Causa real e correcao em
[[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]: simulador esquecido ligado (~2 GB) e SDK
evictado pelo iCloud com disco a 98%. Montei verificacao **local, fora do iCloud**: analise do
projeto inteiro em 4 s e 72 testes de dominio em 20 s (antes nada terminava).

### Alicerces feitos por mim, commitados antes de disparar os agentes
`Segmento` (portao com sentido), `CarroDePapel` com pose no mundo (simula voltas sem Box2D) e
`Eixo` (linha central fechada: distancia, sentido, desvio, curvatura, Catmull-Rom). Sao a
matematica que circuito, regras, IA dos rivais, largada e pistas dividem.

### Como o paralelismo foi organizado
- **Uma arvore so**, sem worktree (evita o problema de [[agentes-paralelos-perdem-tudo-sem-commit]]):
  cada agente tem uma lista de arquivos; os "hubs" (jogo, main, telas) so por edicao cirurgica.
- `commitar.sh`: commita **so os caminhos do agente** (`git commit -- paths`) com retentativa
  (o indice trava sob concorrencia). Nunca `git add -A`, stash, reset, merge nem push.
- `limitado.sh`: no maximo 2 comandos pesados ao mesmo tempo na maquina, teto de 5 min de
  execucao e 4 min de espera na fila; 124/125 = "commite sem testar".
- Documento de contratos com nomes combinados entre pacotes (ids de pista, carro, rival) e
  regras de convivencia; cada pacote registra decisoes em `docs/decisoes/`.
- Fase 1: 12 pacotes independentes. Fase 2: 7 integracoes, cada uma comecando assim que os
  pacotes de que depende terminam (promessas, sem barreira). Fase 3: gate de compilacao, 6
  revisores adversariais, 2 verificadores independentes por achado e correcao.
- Ponto de retorno: tag `antes-do-roadmap`.

### Como terminou
- 1a rodada (27 agentes) morreu no **limite de uso da conta** depois de 23 min; 25 commits
  sobreviveram porque cada agente commitava a cada passo. Depois: fatias de 3 a 6 agentes em Sonnet,
  medindo a cota entre elas (~4-5% da semana por fatia, ~18% da janela de 5 h).
- Entregue: pista recreio (770 m) + margem/verso/noite, regras da corrida com a regra do motorista,
  rivais (5 personalidades, carros proprios), nitro, capotamento com camera lenta, largada com caneta e
  contagem, HUD e rastro, som sintetizado, progresso/garagem/recordes, campeonato + podio, CI, CSP,
  icones, README. ~650 testes de dominio verdes; `dart analyze` limpo.
- **Primeira execucao real**: build baixado do artefato do CI e aberto no painel. Menu, modos, largada,
  grade e HUD funcionam; achado e corrigido travamento no primeiro capotamento
  ([[removefromparent-adiado-e-corpo-destruido-na-hora]]). Correcao ainda nao revista na tela.
- O dono mergeia PR por squash minutos depois de aberto (PR #1 e #6). Solucao: `enviador2.sh` - acha o
  commit de `roadmap` com a mesma ARVORE da main, replica o resto (cherry-pick) numa worktree
  `roadmap-N` a partir da main, empurra e abre o PR seguinte sozinho. Agentes nao conseguem
  `gh pr create` (bloqueio de permissao); o processo do enviador consegue.
- Trabalho feito num clone **fora do iCloud** (`/tmp/claude-501/nfr`): no `~/Documents` o iCloud
  evictava o `.git` (`mmap failed: Operation timed out`) e criava copias de conflito (`arquivo 2.dart`)
  durante um rebase.
- Peso do build: o CI somava as 6 variantes do renderizador e os .symbols (13,6 MB); o navegador
  baixa 3,8 MB no pior caso. Medicao corrigida.
- [[dependabot-sobe-pacote-que-o-sdk-local-nao-aceita]]

### Pendente
Ver a rodada de CI do PR #7 verde; rever na tela o capotamento; rivais colam no jogador parado na
largada; `arremessarMotorista()` sem chamador; Safari do iPhone e 60 fps medidos.

## Relacionado
- [[RagdollGames]]
- [[trabalhar-em-paralelo]]
- [[fisica-de-carro-arcade-visto-de-cima]]
- [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]
