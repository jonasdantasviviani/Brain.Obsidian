---
tipo: preferencia
titulo: Escolher a tecnologia mais chata que resolve
projeto: [todos]
stack: []
tags: [tipo/preferencia, cerebro/regra]
palavras-chave: [simplicidade, stack, escolha, tecnologia, chata, boring, infraestrutura, decisao, principio]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Escolher a tecnologia mais chata que resolve
## Resumo
Cada peca nova de infraestrutura e uma peca a mais para operar as 3h da manha; o gargalo e tempo de quem escreve o codigo, nao performance.

## Contexto
Principio explicito do [[Heavy]] (doc 22, §1), mas aplicado por Jonas em todos os projetos.

## Detalhe

> **Escolha a tecnologia mais chata que resolve o problema.**
>
> Corolario pratico: **um banco, um servico, uma fila. Nada mais ate doer.**

### Como isso se manifesta nas decisoes reais
- [[monolito-modular-em-vez-de-microservicos]] em vez de servicos distribuidos
- [[eventos-como-fonte-da-verdade]] com tabela append-only, **sem framework de event sourcing**
- [[separar-operacional-de-analitico]] desenhado agora, construido so quando o volume pedir
- Link publico do Heavy em Razor **dentro da propria API**, em vez de mais um projeto
- Sites em HTML+CSS puro, sem build ([[Sites-Estaticos]])

### Como aplicar
Ao propor stack ou arquitetura, prefira o que ja existe no projeto. Peca nova de infra so com
justificativa de dor concreta ja sentida — nao de dor prevista.

## Relacionado
- [[dotnet-10-como-padrao-de-backend]]
- [[monolito-modular-em-vez-de-microservicos]]
- [[separar-operacional-de-analitico]]
