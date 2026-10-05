---
tipo: seguranca
titulo: 10. Hash nas senhas
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [senha, hash, bcrypt, argon2, pbkdf2, salt, md5, sha1, password, credencial, login, cadastro de usuario, autenticacao, trocar senha, recuperar senha]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 10. Hash nas senhas
## Resumo
Senha so entra no banco como hash de algoritmo lento e com sal: BCrypt, Argon2id ou PBKDF2. Nunca MD5/SHA1, nunca em claro.

## Contexto
Regra 10 das 20 obrigatorias.

## Detalhe

### Por que hash rapido nao serve
MD5 e SHA-1 foram feitos para serem **rapidos** — e por isso uma GPU testa bilhoes por segundo.
BCrypt e Argon2 sao lentos de proposito e tem sal embutido, o que quebra rainbow table.

```csharp
// BTech.NFe.Api — PasswordService.cs, feito certo
BCrypt.Net.BCrypt.HashPassword(senha, WorkFactor);
BCrypt.Net.BCrypt.Verify(senhaInformada, senhaArmazenada);
```

### Cuidados que vem junto
- Nunca logar a senha, nem em debug
- Comparacao sempre pela funcao `Verify` do algoritmo (tempo constante), nunca `==`
- Erro de login generico: "usuario ou senha invalidos". Dizer qual dos dois errou entrega quais
  e-mails existem na base → [[15-nao-vazar-dados]]
- Rehash quando o work factor subir

### No seu codigo hoje — achado real
- [[BTech.NFe.Api]]: BCrypt no `PasswordService`, com teste unitario. Certo.
- [[Heavy]]: existe a coluna `senha_hash` nas migrations, mas **nenhum algoritmo de hash
  implementado ainda** — o login com senha ainda nao foi construido. Quando for, use Argon2id
  ou BCrypt desde a primeira linha.

## Relacionado
- [[11-rate-limit-no-login]]
- [[15-nao-vazar-dados]]
