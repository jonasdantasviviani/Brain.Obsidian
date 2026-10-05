---
tipo: padrao
titulo: Abertura de estudio com o logo sendo riscado na tela
projeto: [RagdollGames]
stack: [flutter, dart, python]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [abertura, splash, logo desenhado, logo riscado, hachura, barra de progresso, frases engracadas, som de caneta, web audio, autoplay, capa do jogo, custompainter, drawRawPoints, tinta gasta]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Abertura de estudio com o logo sendo riscado
## Resumo
Logo PNG virado em **riscos ordenados** por um script, desenhado risco a risco num
`CustomPainter` com som de caneta sintetizado - e depois a capa do jogo com barra e piadas.

## Contexto
Feito em 2026-09-18 para toda a serie [[RagdollGames]] a pedido do Jonas (ver
[[todo-jogo-da-serie-abre-com-o-logo-riscado]]). Codigo em `presentation/abertura/` de cada
jogo; fonte unica em `Repos/Games/ragdoll-games/comum/abertura/` com `instalar.sh`.

## Detalhe

### 1. O logo, de imagem para risco
`hub/ferramentas/tracos_do_logo.py` (stdlib pura: `zlib` + `struct` decodificam o PNG):
1. alfa > 110 vira tinta, numa grade reduzida pela metade **com maximo do bloco** - traco fino
   nao desaparece;
2. manchas ligadas (BFS, 8 vizinhos), sujeira abaixo de 6 px descartada;
3. **ordem de leitura**: as manchas grandes (area >= mediana) se agrupam em linhas pelo salto
   entre os centros; as pequenas (olho, risco de impacto, travessao) entram na linha de centro
   mais perto e ficam para o fim dela - primeiro o corpo, depois o detalhe;
4. cada mancha e coberta de hachura em vaivem, **no angulo que da o traco mais longo** (testa
   12 angulos e vence o de maior media) - hachura no eixo errado sai picada como tracejado.

Saida: `List<double>` com `x1, y1, x2, y2` por risco. O logo da Rabisco deu **634 riscos, 19 KB
de Dart e nenhum asset de imagem** - e escala para qualquer tela.

```bash
python3 ferramentas/tracos_do_logo.py site/img/logo.png --dart saida.dart --svg previa.svg
```

### 2. Desenhar
- O progresso e medido em **tinta gasta** (soma dos comprimentos), nao em riscos contados:
  risco curto sai rapido, risco longo demora, a caneta corre sempre igual.
- Um `Float32List` por quadro + `canvas.drawRawPoints(PointMode.lines, ...)`: 634 riscos numa
  chamada de desenho.
- O ultimo risco sai pela metade (interpolado), e a caneta e desenhada na ponta dele.
- Tremor determinista por `(semente, quadro)`, com o quadro trocando a 10 fps.
- A soma da tinta e calculada **dos numeros ja arredondados** que o Dart le - senao o teste que
  confere a regua acusa diferenca.

### 2b. O cartaz que vem depois
Capa **inteira** (nao `cover`: recortar para preencher come o letreiro do jogo) na tela menos o
pe, com `BlendMode.multiply` contra a cor do papel - o branco da arte vira folha e nao ha
retangulo colado. No pe, um veu em gradiente ate o papel, e sobre ele a frase e a barra: e o que
garante leitura sobre qualquer desenho que caia ali.

### 3. Som de caneta (Web Audio, sem arquivo)
Meio segundo de ruido branco em laco -> `bandpass` -> ganho. A cada 55 ms um `Timer` sorteia
frequencia (1300-3700 Hz) e volume: e a aspereza variavel que separa "caneta riscando" de
"chiado de radio". Ver [[som-sintetizado-com-web-audio]].

**Cada fase tem o seu som**: o risco so vale enquanto se desenha. No cartaz, onde nada esta sendo
desenhado, repetir o risco soa errado - ali vai um tique seco de papel (`lowpass` 1100 Hz, 110 ms)
a cada troca de frase.

### 4. Portabilidade entre jogos (o que quase impediu a copia)
- **Tokens `const` num jogo, getter em outro**: com modo escuro as cores sao getters, sem ele
  sao `const`. Ler cor direto fazia o lint pedir `const` num jogo e o compilador recusar no
  outro. Solucao: uma classe `TintaDaAbertura` **so de getters**, que os dois casos aceitam.
- **Ordem alfabetica de import depende do nome do pacote** (`counter_ragdoll` antes de
  `flutter`, `ragdoll_go` depois): o instalador reordena o bloco `import 'package:...'` depois
  de trocar o nome. Ver [[instalador-de-codigo-copiado-entre-repos]].

## Relacionado
- [[RagdollGames]]
- [[todo-jogo-da-serie-abre-com-o-logo-riscado]]
- [[som-sintetizado-com-web-audio]]
- [[foto-de-cena-em-teste-flutter]]
- [[audio-do-navegador-espera-o-primeiro-gesto]]
