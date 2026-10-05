---
tipo: sessao
titulo: Games - ideias e planejamento dos jogos progressivos em Flutter
projeto: [Games]
stack: [flutter, dart]
tags: [tipo/sessao, stack/flutter, dominio/jogos]
palavras-chave: [jogo, game, idle, incremental, clicker, merge, roguelite, prestige, planejamento, documentacao]
origem: claude-code
criado: 2026-09-09
atualizado: 2026-09-09
confianca: alta
---

# Games - ideias e planejamento dos jogos progressivos
## Resumo
Levantamento de 6 ideias de jogo mobile progressivo em Flutter, offline, e a pasta de
planejamento em `~/Documents/Repos/Games` com tudo mapeado em markdown.

## Contexto
Jonas quis criar um jogo em Flutter rodando **local** (sem servidor, sem conta, sem rede) e
pediu ideias de jogos de celular **progressivos** - genero idle/incremental. Depois pediu para
materializar tudo numa pasta de documentacao.

## Detalhe

### O que foi feito
1. Seis ideias levantadas, cada uma com genero, loops, economia inicial, escopo de MVP e riscos.
2. Pasta `~/Documents/Repos/Games` criada, so com documento - o codigo nasce depois em
   `Games/<nome-do-jogo>/` como projeto Flutter proprio.

### Estrutura criada
```
Games/
├── README.md                      # indice, mapa das 6 ideias, ordem sugerida
├── comum/
│   ├── motor-idle.md              # tick, save, ganho offline, prestige, numeros grandes
│   ├── stack-flutter.md           # camadas, pacotes, qualidade, nota de seguranca
│   └── design-de-progressao.md    # as 3 engrenagens, curva de custo, ritmo da 1a sessao
└── ideias/
    ├── 01-frota.md                # idle de logistica          ⭐⭐   recomendado
    ├── 02-cozinha-infinita.md     # idle + merge               ⭐⭐⭐
    ├── 03-deep-miner.md           # idle de profundidade       ⭐⭐
    ├── 04-torre-de-cartas.md      # roguelite deckbuilder      ⭐⭐⭐⭐
    ├── 05-jardim-de-automatos.md  # factory / puzzle           ⭐⭐⭐⭐
    └── 06-um-botao.md             # incremental minimalista    ⭐    fazer primeiro
```

O que e comum aos seis saiu das ideias e virou `comum/`, para o motor estar descrito num lugar
so e cada ideia referenciar em vez de repetir.

### Ordem definida (nao e a ordem de esforco)
**Um Botao → Frota.** O Um Botao existe para ser laboratorio do motor: um fim de semana e o
tick, save, offline, prestige e numero grande ficam testados num jogo minusculo, que as ideias
1, 2 e 3 reaproveitam inteiro. Criterio de pronto do motor: **dar para trocar a lista de
upgrades por um arquivo de dados sem tocar na logica**.

### Comandos que funcionaram
```bash
mkdir -p ~/Documents/Repos/Games/{ideias,comum}

# validar todo link relativo .md da pasta
cd ~/Documents/Repos/Games
for f in $(find . -name "*.md"); do d=$(dirname "$f"); \
  grep -oE '\]\([^)]+\.md[^)]*\)' "$f" | sed -E 's/^\]\(//; s/\)$//; s/#.*$//' | \
  while read -r l; do [ -f "$d/$l" ] || echo "QUEBRADO: $f -> $l"; done; done
```

### Resultado
11 arquivos markdown, todos com o frontmatter do protocolo e ligados por link relativo -
verificados, nenhum quebrado. Nenhuma linha de codigo Dart ainda.

## Relacionado
- [[Games]]
- [[motor-idle-em-flutter]]
- [[flutter-puro-sem-engine-nos-jogos]]
- [[flutter]]
