---
tipo: decisao
titulo: Separar o operacional do analitico (mas so construir quando doer)
projeto: [Heavy]
stack: [postgresql, datalake]
tags: [tipo/decisao, stack/postgresql]
palavras-chave: [operacional, analitico, data lake, data warehouse, relatorio, performance, engenharia prematura]
origem: claude-code
criado: 2026-01-01
atualizado: 2026-09-07
confianca: alta
---

# Separar o operacional do analitico (mas so construir quando doer)
## Resumo
Relatorio pesado no mesmo banco que atende o motorista engasga a operacao; desenhar a separacao agora, construir quando o volume pedir.

## Contexto
Decisao do [[Heavy]] (doc 07). O dono trabalha com data lake no dia a dia — a tentacao de
construir cedo demais e real, e por isso a decisao inclui o *quando*.

## Detalhe

| | Operacional | Analitico |
| --- | --- | --- |
| **O que** | dia a dia: motorista marca entrega, ping chega, cliente abre o link | relatorios pesados: custo/km do ano, comparar frota, cruzar meses |
| **Perfil** | rapido, responde na hora | mexe em montanhas de dados de uma vez |
| **Onde** | banco operacional enxuto e veloz | data lake / data warehouse |

### Acesso
A base analitica e **bastidor de engenharia** — o cliente nunca toca nela. Ele ve relatorios
prontos e o sistema busca la por baixo.

### Quando construir
> Desenhar agora, **construir quando o volume pedir**.

Data lake com poucos clientes e matar mosquito com canhao: caro e atrasa o lancamento.
No comeco, um banco bem feito aguenta relatorio tranquilo. Separa quando o volume pedir — ja
preparado, porque pensou nisso desde cedo.

## Relacionado
- [[Heavy]]
- [[eventos-como-fonte-da-verdade]]
- [[tecnologia-mais-chata-que-resolve]]
