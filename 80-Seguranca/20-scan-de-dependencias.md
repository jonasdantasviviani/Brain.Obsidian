---
tipo: seguranca
titulo: 20. Scan de dependencias
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [dependencia, vulnerabilidade, cve, dependabot, npm audit, snyk, trivy, supply chain, atualizacao, pacote, biblioteca, atualizar, nuget, pipeline, github actions]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 20. Scan de dependencias
## Resumo
CI falha quando uma dependencia tem vulnerabilidade conhecida, e as atualizacoes chegam por PR automatico.

## Contexto
Regra 20 das 20 obrigatorias. **Nenhum projeto seu tem isso hoje** — e a regra mais barata de
resolver e a que mais rende.

## Detalhe

### Por que essa e a de melhor custo-beneficio
A maior parte do codigo que voce roda em producao nao foi voce que escreveu. Uma CVE critica
numa dependencia transitiva e tao explorada quanto um bug seu — com a diferenca de que o exploit
ja esta publicado.

### O basico, por stack
```bash
dotnet list package --vulnerable --include-transitive   # .NET
npm audit --audit-level=high                            # Node
flutter pub outdated                                    # Flutter
```

### Dependabot — 6 linhas resolvem
`.github/dependabot.yml`:
```yaml
version: 2
updates:
  - package-ecosystem: nuget
    directory: /
    schedule: { interval: weekly }
  - package-ecosystem: npm
    directory: /
    schedule: { interval: weekly }
```

### No CI, para falhar de verdade
```yaml
- name: Scan de dependencias
  run: dotnet list package --vulnerable --include-transitive | tee /tmp/v.txt
- run: '! grep -q "has the following vulnerable" /tmp/v.txt'
```

### Estado hoje
| Projeto | CI | Dependabot |
| --- | --- | --- |
| [[BTech.NFe.Api]] | `ci.yml` | nao |
| [[ICook]] | `ci.yml` | nao |
| [[BTech.Web]] | nao | nao |
| [[Heavy]] | `azure-pipelines.yml` | nao |

Dois ja tem workflow: adicionar o passo de scan e trabalho de minutos.

## Relacionado
- [[Checklist-Seguranca]]
- [[01-esconder-api-keys]]
