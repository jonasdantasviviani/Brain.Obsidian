---
tipo: padrao
titulo: Mundo cilindrico - girar 360 graus em volta do jogador com fisica 2D plana
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/padrao, stack/flutter, dominio/jogos]
palavras-chave: [360 graus, cilindro, faixa, giro, visada, bussola, realidade aumentada, teleporte, wrap, mundo circular, forge2d, fisica 2d, seta fora de quadro, arremesso, flick]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# Mundo cilindrico com fisica 2D
## Resumo
Para um mundo "em volta de voce" (Ragdoll GO, versao A: sem GPS) nao precisa de 3D: a fisica
continua 2D plana numa faixa de `C` metros, e um objeto `Cilindro` fecha o circulo so na hora de
medir distancia e de teleportar o que ficou meia volta para tras.

## Contexto
`ragdoll-go`, 2026-09-12. O bicho tinha de estar ancorado no espaco (fora de quadro, seta,
girar o corpo) e ao mesmo tempo ser ragdoll na Box2D, que so conhece plano.

## Detalhe

### A conta
```dart
class Cilindro {
  const Cilindro({this.circunferencia = 20});
  final double circunferencia;
  double normalizar(double x) { final r = x % circunferencia; return r < 0 ? r + circunferencia : r; }
  // Menor caminho de `de` ate `para`, em [-C/2, C/2). Decide o lado da seta.
  double diferenca({required double de, required double para}) {
    final bruto = normalizar(para - de);
    return bruto > circunferencia / 2 ? bruto - circunferencia : bruto;
  }
}
```

### A cada quadro
```dart
_visada = cilindro.normalizar(_visada + giro);           // camera.viewfinder.position.x
final alvo = _visada + cilindro.diferenca(de: _visada, para: corpo.x);
if ((alvo - corpo.x).abs() > 0.001) corpo.deslocar(alvo - corpo.x); // setTransform em cada osso
```
O teleporte e sempre de uma volta inteira e so acontece com o que esta **atras** do jogador,
entao ninguem ve.

### As tres regras que fazem funcionar
1. **Chao cobre visada +- meia volta**: com a visada em `[0, C)`, o chao estatico vai de `-C/2`
   a `3C/2` (usei `Polygon.box(22, 0.2)` centrado em 10 para `C = 20`).
2. **Todo desenho periodico tem periodo divisor exato de `C`** - margem a cada 2,5 m, hachura a
   cada 0,25 m. Senao, quando a visada volta de `C` para 0, a folha inteira **salta**.
3. **Detecao de "fora de quadro"** com `diferenca(...).abs() > larguraVisivel / 2`, e o sinal
   dela da o lado da seta. Sem o menor caminho, bicho 5 graus a esquerda parece 355 a direita.

### Entrada: um gesto so
Arrastar para o lado **gira**, jogar o dedo para cima **arremessa** - decidido por
`dy < 0 && |dy| >= |dx|` no dominio (`Arremesso.ehArremesso`). O giro acontece durante o
arrasto; o arremesso so no `onPanEnd`, com velocidade = deslocamento / duracao, limitada.

### Custos e limites
- Bicho que foge **tem de fugir em rajadas** (corre 2,5 s, bufa 1,6 s): num cilindro, fuga
  continua da voltas e a cacada nunca acaba.
- Nascer fora de quadro a `0,7 x largura + sorteio` garante o beat "gire para achar".

## Relacionado
- [[RagdollGames]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[raycasting-em-flutter]] - o outro jeito de "primeira pessoa sem 3D"
