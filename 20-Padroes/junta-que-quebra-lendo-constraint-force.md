---
tipo: padrao
titulo: Junta que quebra na Box2D v3 - ler constraintForce e destruir
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [junta, quebrar, breakable joint, weld joint, constraintForce, box2d v3, forge2d, ragdoll, motorista, capotamento]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Junta que quebra na Box2D v3
## Resumo
A Box2D nao tem junta que arrebenta sozinha; o que ela tem e `joint.constraintForce`, e ler
isso a cada passo e destruir acima de um limiar **e** a junta que quebra.

## Contexto
`Need for Ragdoll`, dia 7: o motorista ragdoll e preso ao banco por juntas fracas que a batida
forte arrebenta - "ele voa, e a corrida continua sem ele". E a mecanica assinatura da serie
inteira (capotar em Borrao, tropecar em Margem).

## Detalhe

```dart
_junta = mundo.physicsWorld.createWeldJoint(
  WeldJointDef(
    bodyA: chassi,
    bodyB: osso,
    localAnchorA: chassi.localPoint(osso.position),  // ancora de onde o osso ja esta
    localAnchorB: Vector2.zero(),
    referenceAngle: osso.angle - chassi.angle,       // sem isto a junta nasce torcida
    linearHertz: 12, angularHertz: 12,               // mola fraca: ele balanca no banco
    linearDampingRatio: 0.7, angularDampingRatio: 0.7,
  ),
);

// a cada passo de fisica, dentro do passo fixo:
if (junta.isValid && junta.constraintForce.length > forcaDeRuptura) {
  junta.destroy();   // seguro durante o step: a destruicao e adiada para o fim
}
```

Pontos que custam tempo se esquecidos:

- **`referenceAngle`** e o angulo de B relativo a A que conta como zero. Criando o boneco com
  um giro proprio (sentado num carro que aponta para qualquer lado), sem esse campo a junta
  nasce torcida e chuta tudo no primeiro passo.
- **`destroy()` durante o `step` e seguro** - o forge2d adia para o fim do passo - mas
  `isValid` tem de ser checado antes de ler qualquer coisa da junta.
- **Ancora de onde o corpo ja esta** (`bodyA.localPoint(bodyB.position)`) evita descrever a
  geometria do banco osso a osso. Cria-se o boneco ja sentado e a amarra so congela a pose.
- Mola fraca (12 Hz) ainda arrebenta: `F = k·x`, e com `k = m·(2π·12)²` um deslocamento de 2 cm
  ja passa de 900 N. Curva forte da ~140 N, batida em cheio passa de 1700 N - **a janela entre
  os dois e onde o limiar mora**.

### Calibrar o limiar
Estime em `F = m·a`, com `m` = massa do osso amarrado:
- em curva no limite, `a` e a aceleracao lateral do carro (~16 m/s2);
- na batida, `a` e a velocidade dividida pelo tempo de parada (~200 m/s2 a 20 m/s).

Se essas duas contas derem numeros proximos, o problema nao e o limiar: e a **massa** estar em
escala errada - ver [[ragdoll-leve-demais-ao-lado-do-veiculo]].

## Relacionado
- [[ragdoll-em-flutter-com-forge2d]]
- [[fisica-de-carro-arcade-visto-de-cima]]
- [[ragdoll-leve-demais-ao-lado-do-veiculo]]
