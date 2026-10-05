---
tipo: projeto
titulo: btech-nfe-web (historico das telas NFe)
projeto: [btech-nfe-web, BTech.Web]
stack: [next, react, typescript, tailwind]
tags: [tipo/projeto, stack/next, empresa/btech, tipo/frontend]
palavras-chave: [btech, nfe web, telas, relatorios, importacao, wizard, sidebar, dark mode, cnab, pedidos, estoque, financeiro]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# btech-nfe-web (historico das telas NFe)
## Resumo
Repositorio onde as telas do sistema NFe foram construidas; historico de commits vale como mapa de funcionalidades.

## Contexto
`~/Documents/Repos/Sites/btech-nfe-web` · branch `master` · sem remote configurado.
Continuado em [[BTech.Web]].

## Detalhe

### O que ja foi entregue (do historico de commits, do mais recente ao mais antigo)
1. Auditoria de `api.ts` contra os controllers reais (Fase 6)
2. Dark mode de ponta a ponta + polimento de UI
3. Reconciliacao de `api.ts` com os controllers + correcao de achados de code review
4. **8 telas de relatorios** + wizard de importacao de cliente em 3 passos
5. Envio de e-mail de NF-e e exportacao de XML em lote
6. Correcao de selects mostrando valor bruto em vez do rotulo — **49 ocorrencias em 19 arquivos**
7. Sidebar em accordion fiel a arvore do sistema legado + filtros modernos em Pedidos/Notas
8. Tela de importacao com execucao em background (polling automatico)
9. Selects reais em Pedidos, CNAB no Financeiro, admin de importacao/tenants
10. Snapshot da aplicacao completa: dashboard, auth, modulos NFe/Pedidos/Estoque/Financeiro

### Modulos do sistema
Dashboard · Auth · NFe · Pedidos · Estoque · Financeiro (com CNAB) · Admin (importacao, tenants) · Relatorios

## Relacionado
- [[BTech.Web]]
- [[BTech.NFe.Api]]
- [[selects-mostrando-valor-bruto]]
