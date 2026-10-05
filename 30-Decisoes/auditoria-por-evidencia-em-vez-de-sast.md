---
tipo: decisao
titulo: Auditoria de seguranca por evidencia textual, nao SAST
projeto: [Cerebro]
stack: [python, seguranca]
tags: [tipo/decisao, stack/python, seguranca]
palavras-chave: [auditoria, sast, seguranca, grep, evidencia, falso positivo, scanner, 20 regras, automacao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Auditoria de seguranca por evidencia textual, nao SAST
## Resumo
O auditor das 20 regras procura sinais no codigo com regex, em vez de analisar fluxo; roda em segundos e nunca bloqueia a sessao.

## Contexto
Montado em 07/09/2026 junto com as 20 regras obrigatorias.
Precisava rodar **a cada sessao, em todo projeto**, sem instalar nada e sem atrasar o inicio.

## Detalhe

### Escolhido — rastreador por evidencia (`~/.claude/cerebro/auditar.py`)
Le os arquivos de codigo uma vez, roda 20 checagens de regex sobre eles, devolve
`ok / falta / revisar / n-a` por regra.

- Zero dependencia: so `/usr/bin/python3`
- ~2-15 s por projeto, com cache de 6 h
- Resultado alimenta o hook `SessionStart` e o [[Checklist-Seguranca]]

**O limite, dito na cara:** `ok` significa *"achei evidencia"*, nao *"esta correto"*.
Um `AddRateLimiter` no arquivo nao prova que ele foi aplicado no endpoint de login.
O auditor rastreia; quem julga e quem le.

### Descartado — SAST de verdade (CodeQL, Snyk Code, SonarQube)
Analisa fluxo de dados e acha o que regex nao acha.
Mas: minutos por execucao, precisa de build, exige servico/licenca, e integra no CI — nao no
inicio de uma sessao interativa. **Continua sendo a escolha certa para o CI**; ver
[[20-scan-de-dependencias]]. As duas coisas convivem: rastreador na sessao, SAST no pipeline.

### O que a calibragem ensinou
Tres rodadas de falso positivo foram necessarias antes de confiar no resultado:

| Falso positivo | Causa | Correcao |
| --- | --- | --- |
| "sem BCrypt" na [[BTech.NFe.Api]] | procurei so em `Program.cs`; o projeto usa `Startup.cs` | varrer todos os `.cs` |
| "sem hash de senha" no [[Heavy]] | casou com senha de **role do banco** em `00-roles.sql` | exigir contexto de senha de usuario |
| 8 regras de backend faltando no [[BTech.Web]] | e front-end puro, sem servidor proprio | detectar `front_puro` e marcar essas regras como n/a |

Licao: **um auditor que grita demais e desligado no primeiro dia.** Vale mais errar para menos
e ser confiavel do que listar tudo e virar ruido.

## Relacionado
- [[Checklist-Seguranca]]
- [[Cerebro]]
- [[20-scan-de-dependencias]]
