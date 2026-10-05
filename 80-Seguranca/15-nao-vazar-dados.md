---
tipo: seguranca
titulo: 15. Nao vazar dados
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [vazamento, erro, stack trace, mensagem, enumeracao, log, problemdetails, excecao, debug, pii, tratamento, catch, resposta de erro, connection string, credencial na resposta, hangfire, argumento de job, dashboard]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-11
confianca: alta
---

# 15. Nao vazar dados
## Resumo
Erro nao conta segredo: sem stack trace na resposta, sem mensagem que revele se o usuario existe, sem dado pessoal no log.

## Contexto
Regra 15 das 20 obrigatorias.

## Detalhe

### Tres vazamentos diferentes, sempre confundidos

**1. Stack trace na resposta**
Entrega caminho de arquivo, versao de framework e estrutura interna.
Use tratamento central: `UseExceptionHandler` + `ProblemDetails` (a [[BTech.NFe.Api]] tem
`GlobalExceptionHandler`). `UseDeveloperExceptionPage` **somente** dentro de `if (IsDevelopment())`.

**2. Enumeracao por mensagem**
| Vaza | Nao vaza |
| --- | --- |
| "usuario nao encontrado" / "senha incorreta" | "usuario ou senha invalidos" |
| "e-mail ja cadastrado" no cadastro | mensagem neutra + e-mail de aviso |
| `404` para registro alheio, `403` para o proprio | `404` para os dois |

A diferenca de **tempo de resposta** tambem enumera: valide a senha mesmo quando o usuario nao
existe, comparando contra um hash descartavel.

**3. Dado pessoal no log**
Nao logar senha, token, CPF, cartao, corpo inteiro da requisicao. Log estruturado
(Serilog) facilita — e tambem facilita vazar tudo sem perceber.
Regra do [[ICook]]: nenhum log via `Console.WriteLine`/`print`, sempre `ILogger`.

### No seu codigo hoje
A auditoria sinalizou na [[BTech.NFe.Api]] um ponto onde a excecao parece voltar na resposta.
Vale conferir se algum `catch` devolve `ex.Message` direto.

**4. Credencial do servidor na resposta — e em argumento de job** (corrigido em 2026-09-11)
O restore do importador devolvia ao navegador a connection string com usuario `sa` e senha, e o
`iniciar` a passava como argumento do Hangfire — gravada em texto puro em `HangFire.Job` e visivel no
dashboard `/hangfire`, que aceitava qualquer `SuperUsuario` (admin de **um** tenant vendo jobs de
todos). Hoje a resposta e o job levam so `databaseName` e o dashboard exige `admin_sistema`.
Licao: argumento de job em fila persistente (Hangfire, Service Bus) e **dado em repouso** — nunca
segredo nele. Ver [[importador-origem-por-databasename-e-allowlist]].

## Relacionado
- [[05-criptografar-dados-sensiveis]]
- [[17-trim-nas-respostas-de-api]]
- [[10-hash-nas-senhas]]
