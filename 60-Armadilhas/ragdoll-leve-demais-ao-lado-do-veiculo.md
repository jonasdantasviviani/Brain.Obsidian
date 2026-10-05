---
tipo: armadilha
titulo: Ragdoll de meio quilo ao lado de um carro de 600 kg - a amarra nunca arrebenta
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/armadilha, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [densidade, massa, escala, ragdoll, box2d, forge2d, junta, ruptura, kg/m2, motorista]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Ragdoll de meio quilo ao lado de um carro de 600 kg
## Resumo
Densidades perto de 1 num esqueleto 2D dao um boneco de **460 g**; ao lado de um carro de
614 kg ele nao existe na fisica, e nenhuma junta que dependa de forca chega perto do limiar.

## Contexto
O esqueleto de 11 corpos foi ajustado sozinho, sem veiculo, com `densidade` entre 0,6 e 1,2 -
numeros que pareciam razoaveis porque **nada no comportamento denunciava**: gravidade nao
depende de massa, rigidez de junta em hertz nao depende de massa, e `empurrar`/`girar` ja
multiplicavam por massa e inercia. O boneco caia bonito.

O erro so apareceu no dia 7, quando o motorista foi amarrado ao banco: com 0,087 kg no torso,
a forca na amarra numa batida em cheio dava ~100 N contra um limiar de 900 N. **A amarra nunca
arrebentava**, e a piada da serie inteira nao acontecia.

## Detalhe
Densidade no Box2D 2D e **kg por metro quadrado**. Um humano de 1,55 m tem ~0,5 m2 de area no
perfil; para 45-50 kg, a densidade precisa ser da ordem de **100**, nao de 1.

```dart
// antes: boneco de 0,46 kg      // depois: boneco de 46 kg
densidade: 1.2,                   densidade: 120,   // torso
densidade: 0.6,                   densidade: 60,    // cabeca
```

Conferir com uma conta de uma linha, sempre que houver mais de um corpo no mundo:
```python
massa = sum(2*meiaLargura * 2*meiaAltura * densidade for osso in ossos)
```

**A regra pratica:** a massa so importa quando dois corpos de origens diferentes se encontram.
Enquanto o ragdoll era o unico objeto da cena, qualquer escala funcionava; no instante em que
ele divide o mundo com um carro, a razao entre as massas passa a mandar em tudo - colisao,
forca de junta, quem empurra quem. **Escolher a escala de massa no mesmo momento em que se
escolhe a escala de tamanho** evita a descoberta tardia.

## Relacionado
- [[junta-que-quebra-lendo-constraint-force]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[fisica-de-carro-arcade-visto-de-cima]]
