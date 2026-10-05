---
tipo: sessao
titulo: Need for Ragdoll - carro, motorista solto, menu e ajustes
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [carro, drift, derrapagem, circulo de atrito, motorista, amarra, weld joint, ruptura, vista de cima, patio de manobras, teclado, toque, comando]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Need for Ragdoll - carro e motorista
## Resumo
Dias 6 e 7 do sprint entregues: o carro com aceleracao, freio, re, esterco e atrito lateral, e
o motorista ragdoll preso ao banco por amarras que a batida arrebenta.

## Contexto
Os dias 1 a 5 ja estavam no lugar (esqueleto, caneta, tremor, consciencia, passo fixo) mais a
abertura da Rabisco. O pedido foi "ande com os proximos passos do jogo", e os proximos passos
eram o dia 6 (carro) e o dia 7 (motorista no banco) de `03-primeiro-sprint.md`.

## Detalhe

### O que entrou
| Camada | Arquivos |
| --- | --- |
| `domain/carro/` | `chassi.dart`, `guidao.dart`, `dinamica_do_carro.dart` |
| `domain/pista/` | `pista.dart` (Pista, Mureta, `Pista.patioDeManobras()`) |
| `domain/entrada/` | `comando.dart` |
| `domain/ragdoll/` | `amarra.dart` |
| `infrastructure/fisica/` | `corpo_carro.dart`, `motorista_no_banco.dart` |
| `infrastructure/entrada/` | `teclado.dart`, `toque.dart` |
| `presentation/jogo/` | `carro_component.dart`, `marcas_component.dart`, `pista_component.dart` |

O jogo passou a ser **visto de cima, sem gravidade** - ver
[[need-for-ragdoll-visto-de-cima-sem-gravidade]].

### As tres coisas que custaram tempo
1. **O modelo de dois eixos nao combina com volante que impoe ω.** Cancelar a velocidade
   lateral no ponto de cada eixo faz o pneu lutar contra a propria curva. Solucao em
   [[fisica-de-carro-arcade-visto-de-cima]].
2. **Derrapagem medida pela saturacao do pneu acusa drift em qualquer curva.** O que vale e o
   angulo de deriva - o carro apontando para um lado e indo para outro.
3. **O boneco pesava 460 g contra um carro de 614 kg**, entao a amarra nunca chegava perto do
   limiar de ruptura - [[ragdoll-leve-demais-ao-lado-do-veiculo]].

### Duas decisoes de arquitetura que vale repetir
- **Forca aplicada dentro do passo fixo.** `MundoFisico` ganhou `antesDoPasso`, chamado antes
  de cada `physicsWorld.step`. Aplicar o motor uma vez por frame, com o `dt` do frame, desfaria
  metade do ganho do passo fixo: o carro acelararia diferente em 30 e em 60 fps.
- **A conta em velocidade, nunca em newtons.** O dominio devolve `deltaFrente`, `deltaLateral`
  e `deltaAngular` em m/s e rad/s; so `CorpoCarro` multiplica por massa e inercia. E o que
  permite testar a fisica inteira sem Box2D.

### Testes
`flutter test`: 43 testes (dominio do carro com um integrador de papel, pista, teclado, toque,
esqueleto, passo fixo, abertura). `flutter analyze` limpo e `dart run import_lint` sem violacao
de camada.

Os testes que tocam a Box2D sairam de `integration_test/` para **`test/fisica/` com
`@TestOn('browser')`**: o runner web so enxerga o que esta em `test/`, e a anotacao faz o
`flutter test` normal pula-los sozinho.

### Segunda leva: menu, ajustes e pausa (PR #1)
Pedido: "faça o menu do jogo, as configurações e etc", depois "commite tudo e abra PR".
- menu no caderno (Correr, Ajustes, Como jogar) com marca-texto no item sob o ponteiro;
  ajustes que se trocam ao tocar (som, traco tremido, marca de pneu, volante, pular abertura);
  pausa com ESC ou dois riscos no canto, corrida parada por baixo
- volante com rampa - [[volante-com-rampa-para-entrada-digital]]
- ajustes no `localStorage` - [[ajustes-no-armazenamento-do-navegador-sem-pacote]]
- tremor desligavel na hora: os componentes dividem um `EstiloDoTraco` mutavel, em vez de
  receber `bool` congelado no construtor
- o app trocou `MaterialApp` por `WidgetsApp` (so `main.dart` usava Material). **Nao tira o
  `ink_sparkle.frag` do build**: o shader vem do pacote `flutter` e entra em todo app
- commit `c18d7e9` na branch `menu-e-ajustes`;
  [PR #1](https://github.com/RabiscoGames/need-for-ragdoll/pull/1) leva os 4 commits que
  estavam so na maquina. Sem CI no repo
- **nao verificado**: analise completa das telas e menu/ajustes na tela - a maquina matava o
  analisador e o `dart test` por falta de memoria ([[impellerc-morto-por-memoria-trava-build-flutter]])

Dois tropecos de git no caminho, ambos por processo morto:
- `index.lock` de 0 byte sobrando de um git que levou SIGKILL - conferir com `lsof` que
  ninguem segura antes de apagar;
- push em segundo plano que parecia falhado terminou depois; o segundo push respondeu
  `cannot lock ref ... reference already exists`. **Isso quer dizer que o primeiro chegou** -
  conferir com `gh api repos/<dono>/<repo>/branches/<branch> --jq .commit.sha`.

## Relacionado
- [[volante-com-rampa-para-entrada-digital]]
- [[ajustes-no-armazenamento-do-navegador-sem-pacote]]
- [[fisica-de-carro-arcade-visto-de-cima]]
- [[junta-que-quebra-lendo-constraint-force]]
- [[ragdoll-leve-demais-ao-lado-do-veiculo]]
- [[need-for-ragdoll-visto-de-cima-sem-gravidade]]
- [[RagdollGames]]
