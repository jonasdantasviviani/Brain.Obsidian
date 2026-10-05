---
tipo: padrao
titulo: Bot que procura o jogador - rota, ronda e ouvido
projeto: [RagdollGames]
stack: [dart, flutter]
tags: [tipo/padrao, dominio/jogos, tema/ia]
palavras-chave: [ia, bot, pathfinding, bfs, patrulha, ronda, memoria, ouvir tiro, grade, navegacao, fps]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-12
confianca: alta
---

# Bot que procura o jogador
## Resumo
Tres pecas resolvem "o bot nunca me acha" num jogo de mapa em grade: **rota** (busca em
largura), **ronda com proposito** e **memoria/ouvido**. Sem qualquer uma delas, mapa grande
fica vazio.

## Detalhe

### 1. Rota: a grade e o grafo
Busca em largura da celula do bot ate a celula destino, guardando `veioDe` e refazendo o
caminho. Diagonal so quando os dois ortogonais estao livres. Rede de waypoints a parte so
compensa com mapa muito maior ou geometria livre.

### 2. Guardar o destino
O erro que custou uma rodada de medicao: refazer a rota periodicamente **sorteando destino
novo** faz o bot vagar para sempre. A rota se refaz; o **destino, nao** - ele so muda quando
o bot chega, quando nao ha caminho, ou quando surge pista melhor.

### 3. Ronda com proposito
Destino uniforme entre as celulas livres quase nunca visita comodo pequeno. Duas correcoes
baratas:
- parte das rondas vai a um ponto **que importa** (nascimento do inimigo, objetivo);
- o resto escolhe o **mais distante entre N sorteios**, o que faz a ronda varrer em vez de
  girar perto de onde ja esta.

### 4. Memoria e ouvido
`ultimoLugarDoJogador` (viu ou ouviu) manda na escolha do destino e e limpo ao chegar. Tiro
dentro de um raio avisa todo mundo - e a informacao que move a partida quando o jogador
esta parado.

### Como saber se funcionou
Medir, nao achar: jogador **parado** no nascimento, quanto tempo ate o primeiro dano, por
cenario e por semente. Vira teste permanente com um limite generoso.


## Objetivo de time e as armadilhas da rota (2026-09-12)
- Um campo `objetivo` por bot, apontado a cada passo pela regra do modo (sitio da bomba, bomba caida,
  canto de guarda), entra na prioridade de destino: lembranca > objetivo > destino > ronda - e, para
  quem carrega a bomba, objetivo > lembranca. Chegando no objetivo, o bot para de guarda.
- **Rota vazia na mesma celula**: busca em largura da celula do bot ate ela mesma devolve vazio. Se a
  regra de "cheguei" depende de consumir o ultimo ponto da rota, o bot parado a 0,4 do alvo nunca
  chega. Rota vazia com o alvo na mesma celula tem de virar `[alvo]`.
- **Perseguir em linha reta prende**: quem ve o jogador de longe por um corredor nao tem caminho reto;
  perseguir tem de usar a mesma rota da procura.
- Medir com rastreio por bot (posicao, estado, objetivo, tamanho da rota, lembranca) a cada 5 s achou
  os tres problemas em uma rodada.
\n## Relacionado
- [[2026-09-11-counter-ragdoll-navegacao-dos-bots]]
- [[raycasting-em-flutter]]
- [[RagdollGames]]
