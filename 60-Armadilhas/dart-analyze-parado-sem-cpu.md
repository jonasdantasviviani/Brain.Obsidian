---
tipo: armadilha
titulo: dart analyze parado sem gastar CPU segura a cadeia inteira
projeto: [RagdollGames]
stack: [dart, flutter, bash]
tags: [tipo/armadilha, stack/dart, ferramenta, tempo]
palavras-chave: [dart analyze, travado, pendurado, hang, analysis server, timeout, alarm, perl, macos, sem timeout]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: media
---

# dart analyze parado sem CPU

## Resumo
Numa cadeia `format; analyze && flutter test`, o `dart analyze` ficou 5 minutos parado (0,03 s de CPU)
e o comando inteiro estourou o limite. Matar o processo e rodar de novo resolveu na hora.

## Sintoma
Nenhuma saida alem da do script anterior; `ps` mostra o `dart analyze` vivo com CPU quase zero.

## Causa provavel
O servidor de analise ficou esperando alguma coisa (trava ou cache) logo depois de uma edicao grande em
varios arquivos. Nao reproduziu na segunda vez.

## Como evitar
- No macOS nao existe `timeout`; use `perl -e 'alarm 150; exec @ARGV' dart analyze` (vale para
  `flutter test` tambem). O processo morre sozinho e a cadeia segue ou falha visivelmente.
- Se travar: `ps aux | grep "dart analyze"`, `kill` no PID e rode de novo.

## Relacionado
- [[cadeia-com-pipe-esconde-falha-de-build]]
- [[2026-09-12-counter-ragdoll-sniper-e-granadas]]
