---
tipo: padrao
titulo: Motor idle em Flutter - tick fixo, save atomico, ganho offline
projeto: [Games]
stack: [flutter, dart]
tags: [tipo/padrao, stack/flutter, dominio/jogos]
palavras-chave: [tick, game loop, acumulador, passo fixo, save atomico, versao de save, ganho offline, prestige, bignum, notacao cientifica]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: media
---

# Motor idle em Flutter
## Resumo
Cinco pecas que todo jogo progressivo reaproveita - escrever uma vez, num jogo minusculo, e
copiar para os outros.

## Contexto
Levantado no planejamento do [[Games]]. Detalhe completo em
`~/Documents/Repos/Games/comum/motor-idle.md`. Vale para qualquer app com simulacao continua,
nao so jogo.

## Detalhe

### 1. Tick fixo, render livre
Dois relogios separados: simulacao em passo fixo de 100 ms (determinista) e render a 60 fps,
que so le e desenha.
```dart
_acumulado += delta;
while (_acumulado >= _passo) { _mundo.tick(_passo); _acumulado -= _passo; }
```
**Nunca acoplar progressao a framerate** - senao celular fraco progride mais devagar que
celular bom e o balanceamento vira mentira. Com **teto de passos por frame** (~5): voltar de
3 h em background nao pode rodar 108.000 passos num frame; isso e trabalho do ganho offline.

### 2. Save
`hive_ce`, a cada 10 s **e** em `AppLifecycleState.paused`/`detached` - salvar so no `dispose()`
nao funciona, o Android mata o processo sem chamar. **Versao dentro do save** (sem isso a
primeira mudanca de balanceamento quebra quem ja jogava) e **escrita atomica** (`save.tmp` +
rename).

### 3. Ganho offline
`delta = agora.toUtc() - ultimoSalvamento`, limitado a 8 h.
- **Sempre UTC** - fuso e horario de verao quebram o local.
- **Teto obrigatorio**, senao o jogo se joga sozinho e a sessao perde sentido.
- **Mostrar numa tela de boas-vindas** - ganho que nao e visto nao engaja.
- `delta` negativo (relogio voltado) = zero, sem punir. Single-player: trapaca e problema de quem faz.

### 4. Prestige
`moeda = floor(k * sqrt(totalDaRodada / referencia))` - raiz, nao linear: a rodada seguinte tem
de ser bem mais rapida, nao infinita. **Nunca liberar antes de o jogador sentir a parede**
(~30 min por upgrade), senao ele zera sem entender o que ganhou.

### 5. Numeros grandes
`double` estoura em `1e308` e perde precisao muito antes, em soma incremental. Usar `decimal`
ou classe propria `BigNumber { double mantissa; int expoente; }` **desde o primeiro commit** -
refatorar depois e o erro classico do genero. Formatacao curta junto (`1.42 aa`, `3.10 K`).

### Onde o motor mora
Em `domain`, sem Flutter e sem pacote externo. E isso que permite
`test/domain/economia_test.dart` simular 10.000 ticks em milissegundos e conferir a curva -
balanceamento sem teste automatizado e chute.

### Criterio de pronto
Dar para **trocar a lista de upgrades por um arquivo de dados sem tocar na logica**. Se for
preciso mexer no codigo para rebalancear, ainda nao esta separado.

## Armadilhas
- Progressao dentro do `build()` - roda em toda reconstrucao e duplica ganho.
- Rebuild da arvore inteira 10x/s - `BlocSelector` por widget. Idle mal feito queima bateria.
- `DateTime.now()` local no save.

## Relacionado
- [[Games]]
- [[flutter-puro-sem-engine-nos-jogos]]
- [[flutter]]
