---
tipo: armadilha
titulo: Impulso em corpo articulado - massa minuscula e momento repartido
projeto: [RagdollGames]
stack: [flutter, dart, forge2d]
tags: [tipo/armadilha, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [impulso, massa, ragdoll, box2d, forge2d, applyLinearImpulse, junta, momento, tuning, fisica]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: alta
---

# Impulso em corpo articulado
## Resumo
Dois erros seguidos ao empurrar um ragdoll: impulso cru manda o boneco para a estratosfera, e
impulso num osso so quase nao move o conjunto.

## Contexto
Ao dar vida ao boneco do [[RagdollGames]] em `need-for-ragdoll`. Os dois erros parecem opostos
e tem a mesma raiz: **ninguem tem intuicao para a massa de um osso**.

## Detalhe

### Erro 1 - impulso cru
```dart
torso.applyLinearImpulse(Vector2(0, -6)); // parece pouco
```
O torso mede 0,26 x 0,28 m com densidade 1,2, entao pesa **87 g**. Impulso de 6 N.s em 0,087 kg
da `dv = 6 / 0,087 = 69 m/s`. O boneco sai da tela e nunca volta.

**Sintoma**: o personagem sobe e some, e parece "gravidade quebrada" ou "junta explodindo".

**Correcao**: pedir **velocidade**, nao impulso. `impulso = velocidade * corpo.mass`.

### Erro 2 - impulso num osso so
```dart
torso.applyLinearImpulse(Vector2(3, 0) * torso.mass); // 3 m/s no torso
```
Agora o torso ganha 3 m/s de verdade - **por um instante**. As juntas repartem esse momento com
os outros dez ossos no mesmo passo, e o conjunto (1,1 kg) fica com
`3 * 0,087 / 1,1 = 0,24 m/s`. O boneco praticamente nao se mexe.

**Sintoma**: o empurrao "nao faz nada", e a tentacao e subir o numero ate cair no erro 1.

**Correcao**: dar o quinhao a cada osso.
```dart
for (final corpo in _corpos.values) {
  corpo.applyLinearImpulse(velocidade * corpo.mass);
}
```

### A regra geral
Em corpo articulado, **impulso num link nao e velocidade do corpo**. Para mover o conjunto a
`v`, todo link recebe `v * m_i`. Vale igual para rotacao com `rotationalInertia`.

### O que ainda ficou aberto
Mesmo com o empurrao certo, o boneco **nao tomba**: em pe, as juntas do joelho e do quadril
ficam encostadas no proprio limite (o joelho so dobra para tras) e as canelas sao caixas largas -
o conjunto vira uma pilha estavel que se auto-endireita. Tombar de verdade pede **osso de pe**
e limites mais folgados no estado "solto". E ajuste de feel, nao de fisica.

## Relacionado
- [[RagdollGames]]
- [[forge2d-e-box2d-v3]]
- [[ragdoll-em-flutter-com-forge2d]]
