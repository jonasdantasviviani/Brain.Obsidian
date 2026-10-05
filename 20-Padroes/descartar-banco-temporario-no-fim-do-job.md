---
tipo: padrao
titulo: Banco temporário é descartado pelo job que o usou, não pela tela
projeto: [BTech.NFe.Api]
stack: [sqlserver, dotnet, hangfire]
tags: [tipo/padrao, importador, sqlserver, limpeza]
palavras-chave: [banco temporario, staging, stg_import, drop database, restore, bak, orfao, limpeza, importacao]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Banco temporário é descartado pelo job que o usou, não pela tela

## Resumo
Quem cria o recurso temporário é quem tem de destruí-lo, no `finally` do próprio job. Endpoint de
descarte que depende do painel chamar = banco acumulando para sempre.

## Contexto
No [[BTech.NFe.Api]], cada `.bak` restaurado vira um banco `stg_import_<12 hex>` no mesmo SQL
Server. Existia `DELETE /api/importador/backups/staging/{databaseName}`, mas nada o chamava
sozinho — o Jonas acabou com vários bancos temporários no servidor. Pedido dele: "após importar
quero apenas ter o banco do sistema, fiel e único".

## Detalhe

### No finally, sempre — inclusive em erro
Os dados já foram gravados no tenant; manter o staging depois disso não ajuda ninguém. E a
falha do descarte **não** pode invalidar a importação: vira aviso no log, não exceção.

### Só o que a própria restauração criou
A trava é o formato do nome (`stg_import_` + 12 hex), reconferido antes de todo `DROP`. Numa
importação por connection string ao vivo **não há nada a descartar** — o banco é o de produção do
cliente, e é exatamente o caso em que dropar seria catastrófico. Tem teste só para isso.

### Limpeza em massa é manual de propósito
Um `DELETE` que dropa todos os `stg_import_*` de uma vez **não** deve ser chamado
automaticamente antes de uma nova restauração: mataria o banco de outra importação rodando em
paralelo. Fica como ação explícita, ao lado de um `GET` que lista o que sobrou (nome, data de
criação, tamanho em MB) — em condição normal, vazio.

## Relacionado
- [[importador-mostra-o-que-nao-importou]]
- [[banco-em-branco-com-importacao-por-painel]]
- [[BTech.NFe.Api]]
