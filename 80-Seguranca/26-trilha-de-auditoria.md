---
tipo: seguranca
titulo: 26. Trilha de auditoria imutavel
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [auditoria, audit, log, rastreabilidade, quem fez, historico, append-only, compliance, lgpd, disputa, canhoto, fiscal, trabalhista]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 26. Trilha de auditoria imutavel
## Resumo
Quem fez o que, quando e de onde — em registro append-only que a propria aplicacao nao pode reescrever.

## Contexto
Regra 26. **Nenhum dos projetos tinha isso até 2026-09-16.** E o [[Heavy]] precisa por definicao: os
proprios docs do produto citam disputa de canhoto e defesa fiscal/trabalhista como requisito
([[eventos-como-fonte-da-verdade]] resolve metade do caminho).

### Status em [[BTech.NFe.Api]] (2026-09-16) — parcial, não fechar esta regra ainda
Implementado (`AuditoriaActionFilter` + `AuditoriaService`, PR
[#36](https://github.com/B-Tech-Sistemas/BTech.NFe.Api/pull/36)): grava automaticamente em
`ArqMorto` (tabela legada, já `ITenantScoped`) toda mutação HTTP (POST/PUT/PATCH/DELETE) que
**terminou com sucesso** — quando, quem (usuário + tenant), tabela, função, descrição com id e
"alvo" tentado por reflection. Ver [[BtechPlus.Gabriel]] pela origem do padrão.

**Continua faltando** (não fechar a regra 26 achando que está pronta):
- Login/logout — o filtro ignora `/api/auth/` de propósito (não tem usuário autenticado ainda);
  precisa de um registro dedicado, e **login falho especialmente**, que é o sinal mais útil.
- Falha não é registrada — o filtro só grava 2xx/3xx. "Tentativa negada" (403/401 num endpoint
  sensível) não deixa rastro nenhum hoje.
- Antes/depois por campo — a descrição é um resumo humano, não um diff estruturado dos campos que
  mudaram (fica difícil provar "o que era antes" numa disputa).
- IP/user agent — "de onde" não é capturado.
- Grants de banco — não foi feito `DENY UPDATE, DELETE` pro login da aplicação em `ArqMorto`; hoje
  a imutabilidade depende só de nenhum código chamar update/delete, não de permissão negada no SQL
  Server.
- Encadeamento por hash — não feito (opcional, só se precisar resistir a contestação formal).

## Detalhe

### Log de aplicacao nao e trilha de auditoria
| | Log (Serilog) | Trilha de auditoria |
| --- | --- | --- |
| Para que | depurar | provar |
| Quem le | desenvolvedor | auditor, juiz, cliente |
| Retencao | dias | anos |
| Pode sumir? | sim, rotaciona | nao |
| Formato | texto livre | estruturado e estavel |

Serilog atende o primeiro. O segundo precisa de tabela propria.

### O minimo por registro
`quando` (UTC) · `quem` (id do usuario **e** do inquilino) · `de onde` (IP, user agent) ·
`o que` (acao) · `sobre o que` (entidade + id) · `antes` e `depois` nos campos que mudaram ·
`resultado` (sucesso ou falha, com o motivo).

Falha conta tanto quanto sucesso: **tentativa de acesso negada** e o sinal mais util que existe.

### O que precisa entrar
Login e logout (inclusive falhos) · mudanca de permissao e de papel · leitura e alteracao de dado
pessoal ([[05-criptografar-dados-sensiveis]]) · operacao financeira e fiscal · exportacao em
massa · uso de endpoint administrativo.

### Imutavel de verdade
- Tabela **append-only**: `GRANT INSERT, SELECT` para a aplicacao, **sem** `UPDATE` nem `DELETE`
- No Postgres, RLS que impede ate o inquilino de ver a trilha de outro
- Retencao definida por lei do dominio (fiscal costuma ser 5 anos)
- Encadeamento por hash quando precisar resistir a contestacao: cada linha carrega o hash da
  anterior, e alterar uma quebra a cadeia

### Nao registre o segredo
Trilha guarda **que** a senha foi trocada, nunca a senha. Vale para token, chave e cartao.
Ver [[15-nao-vazar-dados]].

### Onde encaixa no [[Heavy]]
A decisao de [[eventos-como-fonte-da-verdade]] ja prevê tabela append-only para Ordem/Jornada.
A trilha de auditoria e a mesma ideia estendida para **acao de usuario**, nao so para evento de
dominio. Construir junto sai mais barato que depois.

## Relacionado
- [[eventos-como-fonte-da-verdade]]
- [[15-nao-vazar-dados]]
- [[Heavy]]
