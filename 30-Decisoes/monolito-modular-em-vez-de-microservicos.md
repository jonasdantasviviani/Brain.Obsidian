---
tipo: decisao
titulo: Monolito modular em vez de microservicos
projeto: [Heavy]
stack: [dotnet, arquitetura]
tags: [tipo/decisao, stack/dotnet]
palavras-chave: [monolito, modular, microservico, microservicos, arquitetura, decisao, escala, modulo]
origem: claude-code
criado: 2026-01-01
atualizado: 2026-09-07
confianca: alta
---

# Monolito modular em vez de microservicos
## Resumo
Com um desenvolvedor e zero clientes, servicos separados sao custo puro; monolito modular com fronteiras marcadas permite extrair depois.

## Contexto
Decisao do [[Heavy]] (doc 07). Vale como default para qualquer produto novo em estagio inicial.

## Detalhe

### O raciocinio
Servicos separados multiplicam por N: deploy, observabilidade, consistencia distribuida e
debugging — **sem nenhum ganho** enquanto nao ha escala nem equipe.

### O desenho escolhido
Monolito modular com fronteiras de modulo bem marcadas:
- schema separado por modulo
- comunicacao por eventos internos

Isso permite **extrair um servico depois**, quando um modulo especifico pedir escala diferente.
Na pratica, no Heavy, esse modulo sera so a **ingestao de ping**.

### Quando revisitar
Quando um modulo especifico demonstrar necessidade de escala independente — nao antes.

## Relacionado
- [[Heavy]]
- [[tecnologia-mais-chata-que-resolve]]
- [[separar-operacional-de-analitico]]
