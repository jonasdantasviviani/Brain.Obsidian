---
tipo: armadilha
titulo: Campo privado no construtor vira parametro nomeado PUBLICO, sem o sublinhado
projeto: [RagdollGames, todos]
stack: [dart, flutter]
tags: [tipo/armadilha, stack/dart]
palavras-chave: [this._campo, parametro privado, named parameter, private field formal, too many positional arguments, copyWith, dart 3]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-23
confianca: alta
---

# `this._campo` no construtor cria o parametro `campo`

## Resumo
Declarar `this._bitola` na lista de parametros nomeados cria um parametro
chamado **`bitola`** - publico, sem o sublinhado. Quem chama de fora escreve
`bitola: 0`, e quem escreve `_bitola:` ou passa posicional leva erro.

## Contexto
`need-for-ragdoll`, 2026-09-23, escrevendo um `copyWith` para o `Chassi`. Como
o campo e privado, assumi que so daria para copia-lo posicionalmente. Os
**quatro** jobs do CI reprovaram juntos:

```text
lib/domain/carro/chassi.dart:148:15: Error: Too many positional arguments:
0 allowed, but 1 found.
```

Quatro jobs falhando ao mesmo tempo ja e o sinal: nao e regra de lint nem
teste, e o codigo nao compila.

## Detalhe
```dart
class Chassi {
  const Chassi({
    required this.largura,
    this._bitola,          // <- declara o parametro `bitola`
  });

  final double? _bitola;

  Chassi copyWith({double? largura}) => Chassi(
    largura: largura ?? this.largura,
    bitola: _bitola,       // <- pelo nome PUBLICO, mesmo aqui dentro
  );
}

const pequeno = Chassi(largura: 1.6, bitola: 0);  // la fora, igual
```

E a regra dos "private named parameters" do Dart 3: o campo continua privado
para leitura, mas o parametro que ele gera perde o sublinhado. O jeito de
descobrir sem compilador e olhar **como o resto do repo ja chama** o
construtor - a `Garagem` escrevia `bitola: 0` a vista o tempo todo.

## Relacionado
- [[flutter]]
- [[2026-09-23-need-for-ragdoll-escopo-underground-e-ci-verde]]
