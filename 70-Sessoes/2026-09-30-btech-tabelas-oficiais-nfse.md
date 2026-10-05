---
tipo: sessao
titulo: Tabelas oficiais da NFS-e Nacional e sugestão IBS/CBS
projeto: BTech
stack: [dotnet, nextjs]
tags: [nfse, reforma-tributaria, focus]
palavras-chave: [cTribNac, cClassTrib, indOp, NBS, Anexo VIII, SVRS]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---
# Tabelas oficiais NFS-e Nacional
PRs: API #56, Web #38 (branch feat/tabelas-oficiais-nfse-nacional-e-rt).
- Fontes oficiais baixáveis sem certificado: Anexos B/VII/VIII em gov.br/nfse (rtc e documentacao-atual) e a página pública https://dfe-portal.svrs.rs.gov.br/Cff/ClassificacaoTributaria (JSON `dadosOriginais` embutido no HTML: CST + cClassTrib). Portal NF-e exige cookie jar (curl -c/-b).
- Script: `tools/gerar_tabelas_nfse.py` (stdlib, leitor xlsx próprio com mesclagens; Anexo VIII usa células mescladas).
- Armadilha: Anexo B perde zero à esquerda do cTribNac (recompor de item/subitem/desdobro); indNFSe vazio em indOp usado no Anexo VIII; 15 linhas sem NBS.
- Testes Unit da main quebrados por `httpClientFactory` no ctor (PR #54). `git commit` travou uma vez (index.lock); kill + remover lock resolveu.
- Front verificado com tsc/lint no clone ~/Code/BTech.Web (worktree + symlink node_modules).
Ver [[BTech]].
