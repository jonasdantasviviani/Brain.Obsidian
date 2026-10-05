---
tipo: padrao
titulo: Teste que toca Box2D vive em test/fisica com @TestOn('browser')
projeto: [RagdollGames]
stack: [flutter, dart, forge2d]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [teste, fisica, forge2d, box2d, wasm, TestOn browser, platform chrome, integration_test, initializeForge2D]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: media
---

# Teste de fisica no navegador
## Resumo
Teste que cria mundo Box2D nao roda no host; em vez de exila-lo em `integration_test/` (que
exige aparelho), poe em `test/fisica/` com `@TestOn('browser')` e roda com `--platform chrome`.

## Contexto
`forge2d` e binding para a Box2D nativa: no host o isolate de teste morre com *Connection
closed before test suite loaded*. A saida obvia e `integration_test/` + aparelho, e foi o que o
`need-for-ragdoll` fazia. So que aparelho e caro: no macOS sem CocoaPods o build iOS nem
comeca, e o alvo do jogo e o **navegador** de qualquer forma.

## Detalhe

```dart
/// test/fisica/corpo_carro_test.dart
@TestOn('browser')
library;

void main() {
  // Obrigatorio na web: sem isto nao ha Box2D para criar mundo nenhum.
  setUpAll(initializeForge2D);
  // ...
}
```

```bash
flutter test                              # pula test/fisica sozinho
flutter test test/fisica --platform chrome
```

Tres coisas que so se descobre tentando:

- **o runner web so enxerga o que esta em `test/`.** Apontar `--platform chrome` para
  `integration_test/` da `Error when reading 'org-dartlang-app:///integration_test/...':
  File not found` - o caminho sai da raiz servida;
- **`@TestOn('browser')` faz o `flutter test` comum pular o arquivo**, entao o mesmo repo tem
  uma suite rapida de dominio e uma suite de fisica sem misturar as duas nem manter lista de
  exclusao;
- **`await initializeForge2D()` e obrigatorio na web** e no-op em plataforma nativa, entao o
  arquivo continua valendo se um dia rodar em aparelho.

> Verificado ate aqui: o erro de caminho do runner web, e que a suite de dominio continua
> passando com os arquivos anotados. A **execucao** da suite de fisica no Chrome nao pode ser
> concluida na maquina do Jonas em 2026-09-20 por falta de memoria - ver
> [[impellerc-morto-por-memoria-trava-build-flutter]]. Confirmar assim que a maquina permitir.

## Relacionado
- [[ragdoll-em-flutter-com-forge2d]]
- [[impellerc-morto-por-memoria-trava-build-flutter]]
- [[foto-de-cena-em-teste-flutter]]
