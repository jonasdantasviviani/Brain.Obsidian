---
tipo: armadilha
titulo: Random com a mesma semente a cada sorteio devolve sempre o mesmo numero
projeto: [RagdollGames]
stack: [dart]
tags: [tipo/armadilha, stack/dart, dominio/jogos]
palavras-chave: [random, semente, seed, hashCode, sorteio, aleatorio, mira, bot, deterministico, teste]
origem: claude-code
criado: 2026-09-10
atualizado: 2026-09-10
confianca: alta
---

# Random com semente fixa a cada sorteio
## Resumo
`math.Random(bot.hashCode).nextDouble()` dentro de uma funcao chamada a cada tiro nao sorteia
nada: cria um gerador novo com a **mesma semente** e devolve o **mesmo numero** sempre.

## Contexto
No `counter-ragdoll` (2026-09-10) o bot "nunca errava". Jonas relatou: "minha vida so diminui".
Uma simulacao de 20 s no host mostrou -9 de vida por segundo, **todo tiro acertando**, a 6
celulas de distancia. A formula de erro era `distancia/14 > sorte`, e a `sorte` de cada bot era
um numero fixo - para aquele bot, a 6 celulas, sempre dava acerto.

## Detalhe

### O erro
```dart
double _sorte(Bot bot) => math.Random(bot.hashCode).nextDouble() * 0.5 + 0.5;
```
Parece "sorte por bot". Na verdade e uma **constante por bot**: cada bot acerta tudo ou erra
tudo a uma dada distancia, para sempre.

### A correcao
**Um** gerador, criado uma vez, sorteando a cada tiro - e injetavel para teste:
```dart
CerebroDoBot(this.mapa, {math.Random? sorteio}) : _sorteio = sorteio ?? math.Random();
final acertou = _sorteio.nextDouble() < chanceDeAcerto(distancia);
```
Nos testes, um `SorteFixa(0)` (acerta tudo) ou `SorteFixa(0.99)` (erra tudo) deixa o
comportamento deterministico - e o teste que media "tira vida com o tempo" deixou de ser
potencialmente instavel.

### Como pegar isso antes do jogador
**Teste de distribuicao**: 400 tiros com um gerador de semente conhecida, e a taxa de acerto
tem de ficar entre 20% e 80%. Com o bug, dava 100%.

E, mais geral: quando o relato e "X nunca acontece" ou "X sempre acontece" num sistema com
sorteio, **simular e contar** antes de mexer. A simulacao de 20 s no host levou segundos e
mostrou a causa na primeira leitura.

## Relacionado
- [[raycasting-em-flutter]]
- [[RagdollGames]]
