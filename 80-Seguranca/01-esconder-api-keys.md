---
tipo: seguranca
titulo: 01. Esconder API keys
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [api key, chave, secret, segredo, env, variavel de ambiente, hardcoded, token, credencial, key vault, configuracao, integracao, cliente http, conectar, servico externo]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 01. Esconder API keys
## Resumo
Nenhuma chave, token ou senha literal no codigo. Tudo vem de variavel de ambiente ou cofre.

## Contexto
Regra 1 das 20 obrigatorias. Vale para todo projeto, toda sessao.

## Detalhe

### O que a auditoria procura
Padroes de chave real no codigo versionado: `sk-…`, `AKIA…`, `ghp_…`, `xox…`, `AIza…`, JWT
embutido. Placeholders (`__JWT_KEY__`, `SEU_TOKEN_AQUI`) nao contam como violacao.

### Como fazer certo
| Stack | Onde a chave mora |
| --- | --- |
| .NET | `IConfiguration` + variavel de ambiente; producao no **Key Vault** |
| Next / Node | `process.env`, e **nunca** com prefixo `NEXT_PUBLIC_` se for secreta |
| Flutter | `--dart-define`, nunca constante no codigo (o APK e descompilavel) |

Sempre versionar um `.env.example` com as chaves **vazias**, para documentar o que e preciso.

### Por que importa
Chave em repositorio e chave publica: ela fica no historico do git para sempre, mesmo que voce
apague o arquivo depois. Ver [[02-limpar-secrets-do-git]].

### No seu codigo hoje
A [[BTech.NFe.Api]] faz isso certo: `appsettings.Production.json` usa placeholders `__JWT_KEY__`
substituidos no deploy.

## Relacionado
- [[02-limpar-secrets-do-git]]
- [[03-chave-publica-do-banco]]
- [[Checklist-Seguranca]]
