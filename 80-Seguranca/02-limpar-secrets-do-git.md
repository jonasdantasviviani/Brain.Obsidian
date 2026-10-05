---
tipo: seguranca
titulo: 02. Limpar secrets do git
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [git, secret, historico, gitignore, env, vazamento, rotacionar, filter-repo, appsettings, commit, push, subir codigo, repositorio, versionar]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 02. Limpar secrets do git
## Resumo
Nenhum arquivo de segredo rastreado pelo git, e `.gitignore` cobrindo `.env` antes do primeiro commit.

## Contexto
Regra 2 das 20 obrigatorias.

## Detalhe

### O que a auditoria procura
`git ls-files` retornando `.env`, `.env.local`, `*.pem`, `*.pfx`, `*.jks`, `*.keystore`,
`secrets.json`, `key.properties`. E valores reais dentro de `appsettings.*.json` versionados.

### Se um segredo ja foi commitado
1. **Rotacione a credencial primeiro.** Ela ja vazou; limpar o historico nao a torna secreta de novo.
2. Depois limpe o historico (`git filter-repo` ou BFG) e force o push.
3. So entao adicione ao `.gitignore`.

A ordem importa. Limpar o historico sem rotacionar da falsa sensacao de resolvido.

### Excecao documentada
No [[Heavy]], as credenciais do Azurite no `docker-compose.yml` sao publicas e documentadas pela
Microsoft. **E a unica excecao** — nenhuma outra se aproveita dela.

### No seu codigo hoje
`appsettings.Development.json` esta versionado em [[BTech.NFe.Api]], [[Heavy]] e [[ICook]].
Em dev isso e comum e aceitavel, **desde que os valores sejam claramente de desenvolvimento**
(a chave do BTech e literalmente `BTech@DevKey_NotForProduction_MinLength32!!`). O risco e o dia
em que alguem cola um valor de producao ali.

## Relacionado
- [[01-esconder-api-keys]]
- [[Checklist-Seguranca]]
