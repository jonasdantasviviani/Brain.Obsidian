---
tipo: armadilha
titulo: Workflow reutilizavel do GitHub exige a permissao que ele declara, mesmo com o passo pulado
projeto: [RagdollGames]
stack: [github-actions]
tags: [tipo/armadilha, stack/github-actions, area/ci]
palavras-chave: [startup_failure, workflow file issue, permissions, security-events, workflow_call, reusable workflow, osv-scanner, upload-sarif, sem log]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# Workflow reutilizavel exige a permissao que ele declara

## Resumo
Quem chama um `workflow_call` tem de conceder **todas** as permissoes que o job do reutilizavel
declara. Conceder menos nao pula o passo que usaria a permissao: o workflow **nao nasce**, com
`startup_failure`, zero segundo de execucao e nenhum log para ler.

## Contexto
`need-for-ragdoll`, 23/09 a 26/09. O PR #13 tirou `security-events: write` do job `osv` com um
comentario correto ("sem publicar SARIF, a permissao deixou de ser necessaria") e pos
`upload-sarif: false`. Desde entao, **todo PR** tinha um X fixo do workflow `dependencias` ao lado
dos quatro jobs verdes do `ci`. A mensagem era so:

> This run likely failed because of a workflow file issue.

## Detalhe
O reutilizavel `google/osv-scanner-action/.github/workflows/osv-scanner-reusable.yml@v2.6.0`
declara no **job** dele:

```yaml
jobs:
  osv-scan:
    permissions:
      actions: read
      contents: read
      security-events: write   # so o passo de upload usa
```

O GitHub compara essa declaracao com o que quem chama concede **antes de rodar passo nenhum** -
nao ha como ele saber que `upload-sarif: false` vai pular o passo. Com menos permissao, o run
morre no startup.

A correcao e conceder a permissao e deixar o passo pulado: o token ganha um poder que nao usa.

```yaml
  osv:
    permissions:
      actions: read
      contents: read
      security-events: write
    uses: google/osv-scanner-action/.github/workflows/osv-scanner-reusable.yml@v2.6.0
    with:
      upload-sarif: false   # o passo que exigiria Advanced Security nao roda
```

## Como reconhecer
- `gh run list` mostra `startup_failure` com duracao de 0s ou 1s;
- `gh run view <id>` diz "This run likely failed because of a workflow file issue";
- `--log-failed` nao devolve nada, e nao ha annotation de falha pela API;
- o arquivo do workflow esta sintaticamente valido (o `yaml` carrega).

**Mede o custo:** tres dias de X vermelho fixo no PR, que e pior do que parece - um vermelho que
sempre esta la ensina a ignorar o vermelho.

## Relacionado
- [[osv-scanner-reusable-exige-advanced-security]]
- [[ci-verde-nao-ve-pixel]]
- [[RagdollGames]]
