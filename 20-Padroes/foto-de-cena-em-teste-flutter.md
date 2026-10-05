---
tipo: padrao
titulo: Foto de cena fixa em teste Flutter - conferir desenho sem jogar
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/padrao, stack/flutter, dominio/jogos, teste/visual]
palavras-chave: [PictureRecorder, toImage, png, flutter test, runAsync, cena, screenshot, canvas, desenho, verificacao visual, forge2d, PoseOsso, variavel de ambiente]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-12
confianca: alta
---

# Foto de cena fixa em teste Flutter
## Resumo
Para ver **como algo esta desenhado** sem depender de jogar, monte o estado na mao num teste,
desenhe no `Canvas` de um `PictureRecorder` e grave um PNG. Da para olhar com `Read`.

## Contexto
`counter-ragdoll`, 2026-09-11. O bot atirando de perto nao aparecia a tempo no navegador do
painel (cada screenshot leva segundos e o bot mata antes). Com a foto de cena montei tres bots
- de frente mirando, de perfil andando, de perfil atirando com clarao - mais manchas em
alturas diferentes, e achei na hora que a pose de frente parecia "bracos para cima".

## Detalhe
```dart
testWidgets('cena', (tester) async {
  final partida = Partida(mapa);            // estado montado na mao
  partida.bots..clear()..add(Bot(...)..estado = EstadoDoBot.atirando);
  await tester.runAsync(() async {          // toImage precisa de runAsync
    final gravador = ui.PictureRecorder();
    final canvas = Canvas(gravador);
    Visao(partida).desenhar(canvas, const Size(800, 600));
    final imagem = await gravador.endRecording().toImage(800, 600);
    final png = await imagem.toByteData(format: ui.ImageByteFormat.png);
    File('<scratchpad>/cena.png').writeAsBytesSync(png!.buffer.asUint8List());
  });
});
```

- So funciona porque o desenho (`Visao`) recebe o estado e um `Canvas` - nao depende do Flame
  nem do loop. Separar o render do jogo e o que torna isso possivel.
- Texto sai com a fonte de teste (quadradinhos); serve para forma e pose, nao para tipografia.
- E ferramenta de olhar, nao teste de regressao: apagar o arquivo depois (ou virar golden de
  verdade com `matchesGoldenFile`, se valer manter).

### Quando o componente depende de forge2d
`flutter test` nao carrega a Box2D (ver [[forge2d-e-box2d-v3]]), entao componente que le
`Body` nao pode ser fotografado. No `ragdoll-go` (2026-09-12) a saida foi separar:
- `PoseOsso` (nome, x, y, angulo, meias medidas) foi para `domain/ragdoll/`.
- `DesenhoDoBicho(especie, quadro).desenhar(canvas, poses, metrosPorPixel:)` e puro - so
  `dart:ui` + caneta + tokens. O `BichoComponent` do Flame so repassa `corpo.poses`.
- O teste monta as poses do **esqueleto de referencia** (girado para o "tombado") e fotografa
  os seis bichos, de pe e caidos, num PNG so.

Ficou como **teste de fumaca permanente** (desenhar nao pode lancar) que vira ferramenta de olhar
com uma variavel de ambiente:
```bash
FOTO_DOS_BICHOS=/caminho/bichos.png flutter test test/presentation/foto_dos_bichos_test.dart
```
A primeira foto achou na hora dois defeitos que o navegador escondia (o bicho andava para fora
de quadro entre screenshots): a Espiral eram tres bolhas encadeadas e o rabo do Gato era
riscado no eixo errado, virando um tracinho vertical.

## Relacionado
- [[raycasting-em-flutter]]
- [[verificar-jogo-no-navegador-do-painel]]
- [[RagdollGames]]
- [[mundo-cilindrico-com-fisica-2d]]
