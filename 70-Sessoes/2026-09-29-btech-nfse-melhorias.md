---
tipo: sessao
titulo: NFS-e — e-mail, tributação do município, webhook e lote
projeto: BTech
stack: [dotnet, nextjs, focus-nfe]
tags: [nfse, focus]
palavras-chave: [nfse, webhook, email, municipios, lote]
origem: claude-code
criado: 2026-09-29
atualizado: 2026-09-29
confianca: media
---
PRs: BTech.NFe.Api #49 e BTech.Web #32 (branch feat/nfse-email-tributacao-webhook-lote).
- Focus: e-mail `POST /nfse|nfsen/{ref}/email`; tabelas `GET /municipios/{ibge}/itens_lista_servico` e `/codigos_tributarios_municipio`; hooks `POST /v2/hooks` (event nfse/nfsen). Não há tabela do código de tributação nacional.
- Webhook NFS-e: acha nota sem tenant, `SetTenant`, reconsulta (gatilho, não verdade).
- Nada testado contra a Focus real; front sem tsc (sem node_modules).
Ver [[BTech]].
