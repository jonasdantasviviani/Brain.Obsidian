---
tipo: decisao
titulo: Ajustes do jogo no armazenamento do navegador, sem pacote de banco
projeto: [RagdollGames]
stack: [flutter, dart, web]
tags: [tipo/decisao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [ajustes, configuracoes, persistencia, localStorage, hive, hive_ce_flutter, shared_preferences, import condicional, objective_c, build hooks]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Ajustes no armazenamento do navegador, sem pacote
## Resumo
Os cinco ajustes do Need for Ragdoll ficam num item do `localStorage` (via `package:web`, que o
projeto ja tinha); fora da web valem enquanto o app esta aberto.

## Contexto
O doc do projeto previa `infrastructure/save/ # hive`. `hive_ce_flutter` entrou e saiu no
mesmo dia: puxou uma cadeia nativa (`objective_c` com build hook) e **quebrou ate o
`dart test` do dominio**, que passou a precisar compilar hook nativo. Para guardar cinco
valores.

## Detalhe
- Interface `GuardaDeAjustes` no **dominio**; a gaveta concreta em `infrastructure/save/`, com
  o mesmo import condicional que o som ja usava:
  ```dart
  export 'guarda_de_ajustes_sem_web.dart'
      if (dart.library.html) 'guarda_de_ajustes_web.dart';
  ```
- Um item JSON so (`need-for-ragdoll.ajustes`). Lido **campo a campo** em `Ajustes.doMapa`:
  armazenamento local e editavel pelo console, entao tipo errado ou valor desconhecido vira
  padrao, nunca excecao. `try` em volta de tudo, porque aba anonima pode bloquear o acesso.
- Gravar e trabalho de fundo (`gravar(novos).ignore()`): a tela ja mudou.

### Alternativas descartadas
- `hive_ce_flutter`: dependencia nativa pesada demais para o tamanho do problema.
- `shared_preferences` (usado no Counter-Ragdoll): outra dependencia, tambem com plugin
  nativo. Vira opcao quando o app nativo precisar persistir - troca-se so a gaveta.

## Relacionado
- [[som-sintetizado-com-web-audio]]
- [[teste-de-fisica-no-navegador-com-teston-browser]]
