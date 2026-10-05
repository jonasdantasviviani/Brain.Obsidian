---
tipo: seguranca
titulo: 14. Validacao dos inputs
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [validacao, input, fluentvalidation, zod, dataannotations, allowlist, sanitizacao, entrada, payload, endpoint, formulario, campo, receber json, post, criar, salvar, request, cadastrar, controller]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 14. Validacao dos inputs
## Resumo
Toda entrada e validada no servidor por esquema declarado, com allowlist do que e permitido — nao blocklist do que e proibido.

## Contexto
Regra 14 das 20 obrigatorias.

## Detalhe

### Allowlist, nunca blocklist
Listar o que e proibido (`<script>`, `DROP`, `../`) sempre deixa passar a variacao que voce nao
imaginou. Descreva o que e **valido**: tipo, formato, faixa, tamanho maximo, conjunto de valores.

### Onde declarar
| Stack | Ferramenta |
| --- | --- |
| .NET | **FluentValidation** com `AddFluentValidationAutoValidation` (padrao da [[BTech.NFe.Api]]) |
| Next / Node | **zod** no route handler e na server action |
| Flutter | validacao no formulario **e** no backend |

### Sempre no servidor
Validacao no cliente e UX. A mesma regra tem que existir no servidor, porque a requisicao pode
nao vir da sua tela → [[06-auth-no-servidor]].

### Limites que quase sempre faltam
- **Tamanho maximo** de string (senao um campo de nome de 2 MB entope o banco)
- **Tamanho maximo do corpo** da requisicao (`RequestSizeLimit`)
- **Profundidade** de JSON aninhado (evita DoS por parser)
- **Faixa** de paginacao: `pageSize` sem teto vira `select *` da tabela inteira

A [[BTech.NFe.Api]] cobre a paginacao com `PaginationValidationFilter` global — bom padrao.

## Relacionado
- [[08-bloquear-mass-assignment]]
- [[13-queries-parametrizadas]]
- [[16-restringir-upload-de-arquivos]]
