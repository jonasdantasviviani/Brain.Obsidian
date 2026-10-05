---
tipo: stack
titulo: Base legada do BTech (sistema Delphi) — convenções reais
projeto: [BTech.NFe.Api, BTech.Web]
stack: [sqlserver, docker]
tags: [tipo/stack, stack/sqlserver, empresa/btech]
palavras-chave: [legado, BTechPLUSTESTE, Ativo, A/I, CNPJ_CPF, CNPJPuro, mascara, volume, backup legado, sqlcmd, importacao]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-28
confianca: alta
---

# Base legada do BTech — convenções reais
## Resumo
Conferido na base real (`BTechPLUSTESTE`, 230 MB): status de cadastro é `'A'`/`'I'`, documento e CEP vêm **com máscara**, e as tabelas de movimento têm volume (7,4 mil itens de pedido, 7 mil itens de nota).

## Contexto
Levantado em 2026-09-11 a partir do volume Docker `btech-sqlserver-data-backup-legado`.

## Detalhe

### Como olhar sem estragar o backup
```bash
docker volume create btech-legado-temp
docker run --rm -v btech-sqlserver-data-backup-legado:/from:ro -v btech-legado-temp:/to \
  alpine cp -a /from/. /to/
docker run -d --name btech-legado-temp -e ACCEPT_EULA=Y -e MSSQL_PID=Express \
  -e "MSSQL_SA_PASSWORD=BTech@SqlServer2024!" -p 14333:1433 \
  -v btech-legado-temp:/var/opt/mssql mcr.microsoft.com/mssql/server:2022-latest
# consulta: grave o SQL num arquivo, docker cp, e sqlcmd -C -I -i (aspas no -Q viram dor de cabeça)
docker rm -f btech-legado-temp && docker volume rm btech-legado-temp
```
Nunca montar o volume original: o SQL Server escreve nos arquivos ao anexar o banco.

### Convenções encontradas
| Campo | Legado | Sistema novo |
| --- | --- | --- |
| `Clientes.Ativo`, `Fornecedores.Ativo` | `'A'` (67/67 e 7/7) | `'S'`/`'N'` (`AuthService`, `DistribuicaoDfeJob` filtram `= 'S'`) |
| `Formulas.Ativo` | `'S'` | igual |
| `Produtos.Ativo` | `bit` | igual |
| `CNPJ_CPF` | `09.006.020/0001-44` | dígitos |
| `CNPJPuro` (só Clientes/Fornecedores) | `09006020000144` | — |
| `CEP` | `13420-280` | dígitos |

### Campos fiscais (conferido em 2026-09-28)
| Campo | Legado |
| --- | --- |
| `Produtos.CodFiscal` | **o NCM** ("63079010"); `ClFiscal` = tabela de NCMs |
| `CorNotas.ClFiscal` / `CodCFOP` | NCM e CFOP do item |
| `SitTrib` | origem + CST/CSOSN: `"0102"` (Simples), `"060"` (normal) |
| `Produtos.CodComercial` | vazio em todos |
| `CorNotas.Sequencia` | IDENTITY global (13045) |
Ver [[conclusao-tirada-do-schema-sem-olhar-os-dados]].

### Volume por tabela (amostra do cliente)
CorPedidos 7.377 · Movimentacoes 7.662 · CorNotas 6.956 · CabPedidos 2.274 · CabNotas 2.149 ·
Creditos 2.138 · NFe 2.123 · ArqMorto 2.930 · EmpresasXEstoques 619 · Produtos 652 · Clientes 67 ·
Fornecedores 7 · Empresas 1 · Vendedores 1 · IBTP/IBTPNew ~23 mil (tabela de referência tributária).

### Sem FK
O schema legado praticamente não tem FOREIGN KEY (só 3, das tabelas novas de tenant). A ordem da
importação é decidida pelo remapeamento de id, não pelo banco.

## Relacionado
- [[BTech.NFe.Api]]
- [[importador-casava-coluna-pelo-nome-da-propriedade]]
