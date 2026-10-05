---
tipo: sessao
titulo: Ragdoll Games - criacao da serie rabisco e catalogo de franquias
projeto: [RagdollGames, Games]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [ragdoll, rabisco, serie, catalogo, franquia, nome, trocadilho, navegador, web, forge2d, planejamento]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: alta
---

# Ragdoll Games - criacao da serie
## Resumo
Segunda linha de jogos criada e documentada: satira rabisco de grandes franquias com fisica
ragdoll, rodando em navegador e app.

## Contexto
Na mesma sessao do planejamento do [[Games]], Jonas pediu uma segunda serie "do mesmo padrao":
tudo desenhado como folha de papel a caneta, personagem sempre ragdoll, versoes de
Counter-Strike, Need for Speed, GTA, Minecraft e Pokemon GO. Estudio **Rabisco**, serie
**Ragdoll Games**.

Dois ajustes vieram no meio da execucao:
1. **Nomes devem aludir aos originais** - eu tinha batizado com nome neutro por cautela de marca.
   Correcao registrada em [[parodia-precisa-aludir]].
2. **Roda em navegador, e tambem tem versao app.**
3. **Se a franquia e em ingles, a piada tambem** - os trocadilhos em portugues (Contra-Risco,
   Sede de Borrao, Minerascunho) foram refeitos pela formula final: titulo original, uma palavra
   trocada por `Ragdoll`/`Doodle`. Foram **tres voltas** ate o nome certo, todas registradas em
   [[nomes-por-alusao-em-parodia]].

## Detalhe

### O que foi criado
`~/Documents/Repos/Games/ragdoll-games/` - 11 arquivos:

```
README.md                    # a serie, as regras, os 5 primeiros, ordem
catalogo-de-franquias.md     # ~50 franquias -> nome rabisco + risco + piada ragdoll
comum/identidade-rabisco.md  # papel, caneta, hachura, o tremor (boil) a 8-12 fps
comum/motor-ragdoll.md       # 11 corpos, 10 juntas, consciencia, desempenho
comum/web-e-app.md           # CanvasKit, 5 MB, entrada abstraida, hospedagem estatica
comum/nomes-e-marcas.md      # niveis verde/amarelo/vermelho de alusao
jogos/01-counter-ragdoll.md       # tiro tatico
jogos/02-need-for-ragdoll.md      # corrida - fazer primeiro, laboratorio do motor
jogos/03-grand-thief-ragdoll.md   # mundo aberto - projeto de ano, unico nome 🔴
jogos/04-doodlecraft.md           # sandbox em papel quadriculado
jogos/05-ragdoll-go.md            # cacar criatura - versao A, sem GPS
```

O `README.md` da pasta [[Games]] passou a ter **duas linhas**: Progressivos (Flutter puro) e
Ragdoll Games (`flame` + `flame_forge2d`).

### Decisoes tomadas
- **Motor**: aqui `flame` + `flame_forge2d` sao obrigatorios - [[flutter-puro-sem-engine-nos-jogos]]
  nao vale para a serie. Padrao em [[ragdoll-em-flutter-com-forge2d]].
- **Nomes**: formula de uma palavra trocada, na lingua do original. O eixo de risco deixou de ser
  "quanto alude" e passou a ser **quanto material cunhado pelo dono sobrevive a troca** - por isso
  *Ragdoll GO* e tranquilo e *Ragdollrim* nao e. Todo 🔴 nasce com nome reserva.
  [[nomes-por-alusao-em-parodia]].
- **Web e app do mesmo codigo**, com entrada abstraida em `Comando` desde o primeiro commit.
- **Rabichos GO fica na versao A** (camera + bussola, sem GPS): mantem a serie offline e nao
  aciona LGPD.

### Sacadas que valem guardar
- O **tremor do contorno** a 8-12 fps enquanto o jogo roda a 60 e o que faz parecer feito a mao.
- A **consciencia** (multiplicador da forca das juntas) resolve morte, capotamento, tropeco e
  nocaute com uma variavel so.
- **Papel quadriculado justifica a grade** do jogo de blocos - estetica e mecanica se explicando.
- A **borracha como zona que fecha** no battle royale, e a **caneta vermelha do professor** como
  policia: as duas melhores piadas do catalogo.

### Comando que funcionou
```bash
# validar todo link relativo .md da pasta (achou 1 quebrado apos renomear jogo)
cd ~/Documents/Repos/Games
for f in $(find . -name "*.md"); do d=$(dirname "$f"); \
  grep -oE '\]\([^)]+\.md[^)]*\)' "$f" | sed -E 's/^\]\(//; s/\)$//; s/#.*$//' | \
  while read -r l; do [ -f "$d/$l" ] || echo "QUEBRADO: $f -> $l"; done; done
```

### Roteiros e kit de producao (ultima etapa da sessao)
Cada jogo em `jogos/` ganhou **roteiro completo**: abertura segundo a segundo, a primeira sessao
beat a beat numa tabela, estrutura de sessao, elenco, tom do texto, **o momento "uau"** (a cena
que vira gif e vende o jogo) e a progressao de conteudo.

Nasceram tres mecanicas boas **do roteiro, nao do codigo** - a piada virou regra:
- **Need for Ragdoll**: se o motorista cruzar a linha, vale - com ou sem o carro. Da para capotar
  de proposito na ultima curva e arremessar o proprio motorista por cima da linha.
- **Grand Thief Ragdoll**: os tres **furos de fichario** da margem sao tuneis que tiram voce da
  pagina e zeram o nivel de procurado - a saida contra a caneta vermelha do Professor.
- **Doodlecraft**: cavar fundo o bastante atravessa para **o verso da folha**, onde a tinta vazou
  e tudo esta espelhado. Segundo bioma que nao precisa de explicacao.

Pasta `producao/` com o que faltava para comecar a codar: comandos do dia 1, estrutura de camadas
com `import_lint`, design tokens, sprint de 10 dias e checklist de lancamento.

Na linha progressiva, [[Games]], o **Frota** ganhou o beat sheet da primeira sessao que faltava -
em idle o roteiro e o ritmo, e o ponto exato de liberar o prestige e a parede das ~2 h.

### Resultado
26 arquivos markdown e ~2.340 linhas nas duas linhas, links validados, nenhum quebrado.
Ainda sem codigo Dart - proximo passo e `producao/00-como-comecar.md`.

## Relacionado
- [[RagdollGames]]
- [[Games]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[nomes-por-alusao-em-parodia]]
- [[parodia-precisa-aludir]]
