---
tipo: sessao
titulo: "BTech — conferência fiscal da NF-e e tudo cadastrável/digitável; PRs de todas as branches"
projeto: [BTech.NFe.Api, BTech.Web, RagdollGames, ICook]
stack: [dotnet, efcore, sqlserver, focus-nfe, next, react, git]
tags: [tipo/sessao, fiscal, focus-nfe, dados-legados, git]
palavras-chave: [focus 422, codigo_produto, cfop, numero_item, ncm, sittrib, csosn, conferencia fiscal, pendencias, natureza de operacao, classificacao fiscal, prs pendentes, branches]
origem: claude-code
criado: 2026-09-28
atualizado: 2026-09-28
confianca: alta
---

# BTech — conferência fiscal da NF-e; PRs de todas as branches

## Resumo
A Focus devolvia 422 no item; causa nos dados legados. Entregue conferência fiscal (API) + tela
"Conferir e corrigir" e cadastros de Natureza/NCM (Web): Api#44 e Web#26. Antes, PRs de todo
trabalho sem PR nos repositórios.

## Contexto
Pedidos do Jonas: (1) "faça PR de todas as branches para deixar só a main"; (2) erro 422 da Focus
("itens.1.codigo_produto não pode ser vazio, cfop vazio, numero_item muito longo") e "todos os
campos que precisam de cadastro na nota eu quero ter como cadastrar ou incluir na mão".

## Detalhe

### PRs pendentes
Workflow de levantamento (um agente por repo + verificador cético por branch) em 22 repos. Quase
todas as branches BTech já estavam na main por squash (algumas com versões MAIS ANTIGAS de pacote:
abrir PR delas rebaixaria a main). PRs abertos: doodlecraft#1, grand-thief-ragdoll#1,
ragdoll-go#1 (commits só na main local → branch nova + `git branch -f main origin/main`),
counter-ragdoll#1–#6 (4 branches de worktree de agente com nome legível via refspec, fidelidade-fina,
main local), ICook#2. Sem remoto: Sites/btech-nfe-web. Branches já mergeadas ficaram para excluir
com confirmação. Subagentes bateram no limite semanal no meio — o resto foi feito no loop principal.

Depois, a pedido ("abra todos os PRs"), as 5 branches restantes sem PR e já contidas na main
também ganharam PR, com título "[Já na main — fechar e apagar a branch]" e o motivo verificado
(Api#45, Web#27/#28/#29, need-for-ragdoll#19) — o Jonas fecha e apaga pelo próprio GitHub. Sem PR
possível: 2 branches com 0 commits à frente (GitHub recusa) e o `master` do repo pessoal
jonasdantasviviani/BTech.Web (sem histórico em comum).

Com o ok do Jonas: apagadas no GitHub as 2 branches da API com 0 commits à frente e o `master` do
repo pessoal (cópia idêntica no clone stub-backup); e 32 branches locais com prova de merge
([[apagar-branch-mergeada-por-squash-com-prova]]). Mantidas: `security/checklist-20-regras` da API
(sem prova mecânica — conteúdo entrou por PRs de outro nome), branches atuais com alterações não
commitadas (hub, Heavy) e as de PR aberto.

### Fiscal
- Causas e dados reais: [[conclusao-tirada-do-schema-sem-olhar-os-dados]].
- `GET /api/notas-fiscais/{id}/conferencia-fiscal` + emitir recusa com a lista (sem ir à Focus).
- Ordem por campo: item > nota > natureza > produto; NCM cai em `Produtos.CodFiscal`.
- Nota transmitida não edita. Natureza com código sequencial por tenant.
- Web: `EditorNotaFiscal` (nova e corrigir), `lib/fiscal.ts` (origem/CSOSN/CST e formato "0102"),
  cadastros `/dashboard/naturezas-operacao` e `/dashboard/classificacoes-fiscais`, produto com
  origem + CST. Campos que a tela mandava errado: `consumiFinal`, `infAdic` (→ `dadosAd1..12`).

### Verificação
569 unit, 211 contract, 42 functional, 61 integração; nota real do legado copiada para o banco local
e passada por conferência → pendências → correção → pronta, pela API e pelo navegador.

## Relacionado
- [[focus-token-por-empresa-cifrado]]
- [[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]]
- [[validator-espelha-o-required-do-openapi-do-parceiro]]
- [[zsh-nao-separa-variavel-em-palavras]]
- [[BTech.NFe.Api]]
- [[BTech.Web]]
