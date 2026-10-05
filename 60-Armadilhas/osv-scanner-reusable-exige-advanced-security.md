---
tipo: armadilha
titulo: O OSV-Scanner reutilizavel reprova em repo privado - e nao e vulnerabilidade
projeto: [RagdollGames, todos]
stack: [github-actions, seguranca]
tags: [tipo/armadilha, stack/github-actions, area/ci]
palavras-chave: [osv, osv-scanner, sarif, code scanning, advanced security, repositorio privado, upload-sarif, ci vermelho fixo, regra 20, dependencias]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-23
confianca: alta
---

# OSV-Scanner reutilizavel exige Advanced Security

## Resumo
O job `osv` reprovava em **todas** as branches de um repo **privado** - e nao havia
vulnerabilidade nenhuma. Ele morre ao **publicar** o resultado, nao ao escanear.

## Contexto
`need-for-ragdoll`, 2026-09-23. Todo PR nascia com um vermelho, e um vermelho fixo esconde os
vermelhos de verdade - foi o que aconteceu: o `analise` quebrado passou dias no meio do ruido.

## Detalhe
```text
##[error]Please verify that the necessary features are enabled: Advanced Security must be
enabled for this repository to use code scanning.
CODEQL_ACTION_JOB_STATUS: JOB_STATUS_CONFIGURATION_ERROR
```
O `google/osv-scanner-action/.github/workflows/osv-scanner-reusable.yml` faz upload de SARIF
para *Security > Code Scanning* por padrao (`upload-sarif: true`). Code scanning em repositorio
**privado** exige GitHub Advanced Security, que planos comuns nao tem.

### Correcao
```yaml
    with:
      scan-args: |-
        --lockfile=pubspec.lock
      upload-sarif: false
```
O scan **continua reprovando** o PR quando acha vulnerabilidade: `fail-on-vuln` ja e `true` por
padrao. So a publicacao sai. E, sem publicar, `security-events: write` deixa de ser necessario
nas `permissions` - permissao que nao se usa nao se pede.

### Como diagnosticar rapido
O log do job nao diz "sem vulnerabilidade": ele mostra `fail-on-vuln: true`, roda o scan, e so
depois morre no passo do CodeQL. Se o erro fala em **Advanced Security** ou **code scanning**,
o problema e publicacao, nao dependencia.

## Relacionado
- [[20-scan-de-dependencias]]
- [[dependabot-sobe-pacote-que-o-sdk-local-nao-aceita]]
- [[2026-09-23-need-for-ragdoll-escopo-underground-e-ci-verde]]
