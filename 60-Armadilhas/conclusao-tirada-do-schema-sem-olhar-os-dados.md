---
tipo: armadilha
titulo: "Concluir o que a base legada guarda olhando só o schema (o NCM estava lá o tempo todo)"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [sqlserver, dotnet, focus-nfe]
tags: [tipo/armadilha, dados-legados, fiscal]
palavras-chave: [ncm, codfiscal, clfiscal, sittrib, 0102, csosn, schema, base legada, focus 422, codigo_produto vazio, numero_item, restaurar bak]
origem: claude-code
criado: 2026-09-28
atualizado: 2026-09-28
confianca: alta
---

# Concluir o que a base legada guarda olhando só o schema

## Resumo
Em 16/09 concluí que "a base legada nunca teve NCM" procurando a palavra `Ncm` no schema. O NCM
estava em `Produtos.CodFiscal` e `CorNotas.ClFiscal` — nome diferente, mesmo dado.

## Contexto
A Focus recusava a nota com 422 (código do produto vazio, CFOP vazio, `numero_item` longo). Só
restaurando o `.bak` real do cliente e fazendo `SELECT TOP 8` nas colunas fiscais a causa apareceu.

## Detalhe
O que os dados mostraram (BTechPLUSTESTE, 652 produtos, 6956 itens de nota):

| Coluna | Conteúdo real |
| --- | --- |
| `Produtos.CodFiscal` | NCM de 8 dígitos ("63079010") — em 100% dos produtos |
| `ClFiscal.CodFiscal` | tabela de NCMs (65 de 68 com 8 dígitos) |
| `CorNotas.ClFiscal` | NCM do item |
| `SitTrib` | origem + CST/CSOSN num campo só: `"0102"` = origem 0 + CSOSN 102 |
| `Produtos.CodComercial` | vazio nos 652 → `??` não cai no fallback com `""` |
| `CorNotas.Sequencia` | IDENTITY global, até 13045 → não serve de `numero_item` (máx. 3 dígitos) |

Como inspecionar sem tocar no banco do sistema:
```bash
docker cp backups/BTechPLUSTESTE.BAK btech-sqlserver:/tmp/legado.bak
# RESTORE FILELISTONLY para ver os nomes lógicos, depois:
# RESTORE DATABASE LegadoInspecao FROM DISK='/tmp/legado.bak' WITH MOVE 'BTechPLUS' TO '/var/opt/mssql/data/LegadoInspecao.mdf', MOVE 'BTechPLUS_log' TO '/var/opt/mssql/data/LegadoInspecao_log.ldf'
# ao final: DROP DATABASE LegadoInspecao; docker exec -u 0 btech-sqlserver rm /tmp/legado.bak
```
O arquivo `.bak` estava evictado pelo iCloud (`compressed,dataless`); o `docker cp` força o download.

## Lição
Nome de coluna do Delphi não diz o conteúdo. Antes de afirmar "o dado não existe", olhar amostra
dos dados. E string vazia do legado precisa de `Primeiro(...)`/`IsNullOrWhiteSpace`, não de `??`.

## Relacionado
- [[campo-ncm-do-produto-era-codfiscal-e-nem-salvava]]
- [[base-legada-btech-delphi]]
- [[validator-espelha-o-required-do-openapi-do-parceiro]]
- [[2026-09-28-btech-conferencia-fiscal-da-nota]]
