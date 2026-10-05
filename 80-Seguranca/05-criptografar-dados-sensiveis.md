---
tipo: seguranca
titulo: 05. Criptografia de dados sensiveis
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [criptografia, cpf, cnpj, cartao, dados pessoais, lgpd, pgcrypto, mascaramento, at rest, sensivel, cadastro, cliente, motorista, usuario, salvar dados, coluna nova]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 05. Criptografia de dados sensiveis
## Resumo
Dado pessoal sensivel nao fica em claro no banco: criptografa, tokeniza ou mascara.

## Contexto
Regra 5 das 20 obrigatorias. No Brasil isso e LGPD, nao so boa pratica.

## Detalhe

### O que conta como sensivel aqui
CPF, CNPJ de pessoa fisica, RG, numero de cartao, salario, data de nascimento, passaporte,
geolocalizacao ligada a pessoa.

### Escala de protecao, do mais simples ao mais forte
| Nivel | Quando usa |
| --- | --- |
| **Criptografia em repouso do disco/servico** | base, sempre — mas nao protege de quem le o banco |
| **Mascaramento na resposta da API** (`***.456.789-**`) | quando a tela nao precisa do valor inteiro |
| **Coluna criptografada** (`pgcrypto`, Always Encrypted, `IDataProtector`) | dado que o app le mas ninguem mais deveria |
| **Tokenizacao / nao guardar** | cartao de credito: idealmente nunca toca no seu banco |

### No seu codigo hoje
- [[Heavy]]: `cpf`, `cnpj` e `cartao` em claro nas migrations (`20260813005100_Inicial.cs`).
  O CPF do motorista e o caso mais claro para mascarar na resposta e avaliar cripto em repouso.
- [[BTech.NFe.Api]]: CNPJ em claro — aqui e discutivel, porque CNPJ de empresa emitente e dado
  publico da nota fiscal. **Avaliar caso a caso, nao aplicar cegamente.**

### Regra pratica
Se o dado vaza e alguem pode ser prejudicado pessoalmente, criptografe ou nao guarde.

## Relacionado
- [[15-nao-vazar-dados]]
- [[17-trim-nas-respostas-de-api]]
- [[Checklist-Seguranca]]
