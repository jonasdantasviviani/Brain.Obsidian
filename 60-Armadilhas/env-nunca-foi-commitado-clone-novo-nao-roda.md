---
tipo: armadilha
titulo: "\"Pararam de commitar o .env\" — ele nunca foi commitado; quem quebra é o clone novo"
projeto: [BTech.NFe.Api]
stack: [dotnet, docker, powershell, bash]
tags: [tipo/armadilha, env, setup-local, windows, git]
palavras-chave: [env, .env, env file not found, clone novo, maquina nova, windows, run-local-api, setup-local, gitignore, env.example, sumiu do repositorio, nao esta sendo commitado]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# "Pararam de commitar o .env" — ele nunca foi commitado; quem quebra é o clone novo

## Resumo
`Env file not found: ...\.env` numa máquina nova não é regressão: o `.env` **nunca** esteve no
histórico do [[BTech.NFe.Api]]. O que faltava era o script criar o arquivo sozinho — como o front
já fazia.

## Contexto
Rodar o `run-local-api.ps1` num clone Windows falhou com "arquivo env não existe", e a leitura
natural foi "alguém parou de commitar os `.env`". A verificação desmente:

```bash
git log --oneline --all --diff-filter=ADR --name-status -- '*.env' '.env'
# única entrada: f1b5f78  A  .env.example
```

O `.gitignore` cobre `.env` desde o primeiro commit (regra [[02-limpar-secrets-do-git]]), e no Mac
o arquivo existia só porque tinha sido copiado à mão meses antes — `diff .env .env.example` dava
zero, nem segredo real tinha.

## Detalhe

### Por que só apareceu agora
Só quebra em **clone novo**. Quem já tinha o `.env` local nunca viu o erro, então o bug ficou
escondido até a primeira máquina nova (Windows).

### O que enganou
- `docker compose up` **funciona** sem `.env` — todo `${VAR}` no `docker-compose.yml` tem default
  (`${SA_PASSWORD:-BTech@SqlServer2024!}`). Só o `run-local-api.*` abortava.
- O front ([[BTech.Web]]) nunca deu esse erro porque o `scripts/dev-local.ps1` **já** copiava
  `.env.example` → `.env.local` sozinho. A assimetria entre os dois repos era o bug real.

### Correção
`setup-local.{ps1,sh}` e `run-local-api.{ps1,sh}` criam o `.env` a partir do `.env.example` quando
ele não existe, em vez de abortar. Ver [[criar-env-a-partir-do-example-no-script-de-setup]].

### O \r invisível de brinde
Copiar `.env.example` num clone Windows com `core.autocrlf=true` gera um `.env` CRLF. O loader
do `run-local-api.sh` (`while IFS='=' read -r key value`) não tira o `\r`, e a connection string
sai com ele no meio — falha de login no SQL Server sem explicação:

```
Database=BTechPLUSTESTE^M;Password=Senha123!^M;
```

Blindado com `.env.example text eol=lf` no `.gitattributes`. Mesma família de
[[repositorio-oscilando-entre-crlf-e-lf]].

## Como evitar
Antes de aceitar "pararam de commitar X", rode `git log --all -- X`. Se nunca esteve lá, o bug é
de setup, não de commit perdido — e a correção é o script criar o arquivo, nunca versionar o
segredo.

## Relacionado
- [[criar-env-a-partir-do-example-no-script-de-setup]]
- [[02-limpar-secrets-do-git]]
- [[repositorio-oscilando-entre-crlf-e-lf]]
- [[powershell-5-1-le-ps1-sem-bom-como-windows-1252]]
- [[BTech.NFe.Api]]
