---
tipo: sessao
titulo: Ragdoll Games - regra de camadas (import_lint 2.0) ligada de verdade nos jogos
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos]
palavras-chave: [import_lint, analysis_options, regra de camadas, forge2d_so_na_infra, Forge2DGame, JogoComFisica, plugin, diagnostics, need-for-ragdoll, ragdoll-go, sonda, documentos da serie]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Ragdoll Games - import_lint ligado
## Resumo
A regra de camadas da serie nunca tinha rodado. Agora roda no `need-for-ragdoll` e no `ragdoll-go`
com zero violacao, o jogo Flame passou a estender uma base de fisica na infra, e os documentos da
serie ensinam o formato que funciona.

## Detalhe

### Pedido e decisoes do Jonas
Mover as regras do `import_lint.yaml` para o `analysis_options.yaml`, listar as violacoes e **esperar
decisao antes de mexer em codigo**. Decidido: base `JogoComFisica` na infra; plugin + `severity:
"error"`; corrigir o documento em todos os lugares.

### Feito
- Lida a fonte do `import_lint 2.0.0` e do `analysis_server_plugin 0.3.14`.
- `analysis_options.yaml` dos dois: 10 regras (as 3 do documento viraram familias), `except: []`,
  `severity: "error"`, plugin em forma de mapa com `diagnostics: import_lint: true`.
  `import_lint.yaml` apagado nos dois.
- `lib/infrastructure/fisica/jogo_com_fisica.dart` nos dois; `*_game.dart` estende a base e usa
  `criarChao(...)`; nenhum import de `flame_forge2d` na apresentacao.
- Documentos da serie (`ragdoll-games/producao` + `docs/serie/producao` de counter-ragdoll,
  doodlecraft, grand-thief-ragdoll, need-for-ragdoll, ragdoll-go): secao do `01-estrutura` reescrita,
  `00-como-comecar` e `04-checklist` ajustados; README do need-for-ragdoll ajustado.

### Descobertas no caminho
1. Config errada falha **em silencio** (lista com virgula, `package:*/`, nome de pacote copiado) - ver
   [[import-lint-2-config-e-regra-silenciosa]]. O `import_lint.yaml` do `ragdoll-go` ainda apontava
   para `package:need_for_ragdoll/`.
2. O plugin carrega e compila, mas **so reporta com `diagnostics`**, e como `info`; o
   `flutter analyze` nao mostra; `analyzer: errors:` nao reconhece o codigo.
3. Dois comandos Bash paralelos contaminaram um ao outro pelo `cd` - ver
   [[bash-paralelo-compartilha-diretorio]]. Verificacao refeita em sequencia.

### Verificado
| Projeto | sonda no CLI | sonda no `dart analyze` | final: import_lint / dart analyze / flutter analyze | testes |
| --- | --- | --- | --- | --- |
| need-for-ragdoll | error, exit 1 | info `domain_puro_sem_flutter` | limpo / limpo / limpo | 11 |
| ragdoll-go | error, exit 1 | info `domain_puro_sem_flutter` | limpo / limpo / limpo | 32 |

### Pendente
- `counter-ragdoll`: mesmo `import_lint.yaml` solto (regra morta) - nao mexido, fora do pedido.
- `stack-flutter.md` dos Progressivos (6 repos + `comum/`) ainda promete `import_lint.yaml` reprovando.
- Nada commitado.

## Relacionado
- [[RagdollGames]]
- [[import-lint-2-config-e-regra-silenciosa]]
- [[jogo-flame-estende-base-de-fisica-na-infra]]
- [[bash-paralelo-compartilha-diretorio]]
- [[2026-09-12-ragdollgo-captura-jogavel]]
