---
tipo: sessao
titulo: Counter-Ragdoll - muretas, a cobertura que faz o agachar valer
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/sessao, dominio/jogos, jogo/fps]
palavras-chave: [mureta, parede baixa, cobertura, raycasting, z-buffer, ordem de desenho, linha de visao, agachar, balistica]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Counter-Ragdoll - muretas
## Resumo
Parede na altura do peito (`-` no mapa, altura 0,48): para o corpo, o tiro passa por cima e,
agachado atras dela, os bots perdem voce de vista. Commit `3c8d7b7`, 190 testes.

## Detalhe
- **Uma peca, tres regras, um numero so.** A mureta em 0,48 fica entre o olho em pe (0,5) e o
  olho agachado (0,32). Dai saem as tres regras sem constante nova: o tiro reto de pe passa
  raspando, o agachado bate nela, e a linha de visao usa `porCimaDeMureta: !alvoAgachado`.
- **O raio nao para nela:** `lancarRaio(..., muretas: lista)` anota as paredes baixas do
  caminho, em ordem, e segue ate a parede inteira. Sem a lista, mureta para o raio como
  qualquer parede - assim o resto do codigo nao muda.
- **Ordem de desenho:** mureta e desenhada **depois dos bots**, senao ela nao esconde ninguem.
  E precisou de **papel opaco por baixo da hachura**: o preenchimento da parede e translucido
  de proposito, e o bot atras aparecia atraves dela. Achado por foto de cena
  ([[foto-de-cena-em-teste-flutter]]), nao por teste.
- **Mureta fecha passagem** (o corpo nao passa): duas das que coloquei ilharam area e o teste
  dos cenarios pegou na hora - inclusive uma que tapou a porta de uma sala.

## Armadilha do dia
`flutter build web ... | tail -1` **esconde falha**: o codigo de saida vira o do `tail`. A
build falhou (concorrencia com `flutter test` na mesma cadeia) e a cadeia reportou sucesso.
Ver [[cadeia-com-pipe-esconde-falha-de-build]].

## Relacionado
- [[RagdollGames]]
- [[raycasting-em-flutter]]
- [[2026-09-11-counter-ragdoll-mapas-do-1-6]]
