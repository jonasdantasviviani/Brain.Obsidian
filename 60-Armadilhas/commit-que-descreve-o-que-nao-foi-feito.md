---
tipo: armadilha
titulo: Afirmar de memoria o que foi ou nao feito - conferir no git antes
projeto: [RagdollGames, todos]
stack: [git]
tags: [tipo/armadilha, stack/git, cerebro/processo]
palavras-chave: [memoria, afirmacao, git show, git log, commit, verificacao, correcao, nota errada]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Afirmar de memoria o que foi ou nao feito
## Resumo
Numa sessao longa, "lembrar" se uma edicao aconteceu nao e confiavel - nem para dizer que
foi feita, nem para dizer que **nao** foi. Conferir no git antes de afirmar qualquer uma das
duas.

## Contexto
`counter-ragdoll`, 2026-09-11. Uma captura de tela nao bateu com o esperado e eu concluí, de
memoria, que a regra "uma tentativa de trava" **nunca tinha entrado no codigo** e que a
mensagem do commit `9f1bb90` descrevia algo inexistente. Disse isso ao Jonas e escrevi uma nota
no Cerebro sobre "commit que descreve o que nao foi feito".

Estava errado: `git show 9f1bb90:lib/infrastructure/entrada/mouse_cru_web.dart` mostra o
`_jaPediu` la. A edicao tinha ido na mesma chamada do build, e eu me lembrei so da chamada da
nota. O commit estava certo; a acusacao, nao.

## Detalhe

### A regra
- Antes de dizer "isso nao foi feito" ou "isso foi feito", rodar
  `git show <commit>:<arquivo> | grep <trecho>` ou `git log -S '<trecho>'`. Custa um segundo.
- Contradicao entre tela e codigo tem **varias** explicacoes (cache, clique nao entregue, foco,
  o proprio codigo). "O historico mente" e a menos provavel e a mais cara de afirmar.
- Nota do Cerebro so depois de conferido - uma nota errada ensina errado nas proximas sessoes.

### Correcao feita
A nota antiga foi reescrita (esta), e a da sessao corrigida. O defeito que a captura mostrava
era outro e real: o teclado ficava sem foco depois do menu.

## Relacionado
- [[RagdollGames]]
