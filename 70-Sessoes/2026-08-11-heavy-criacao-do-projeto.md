---
tipo: sessao
titulo: Criacao do projeto Heavy do zero, em paralelo
projeto: [Heavy]
stack: [dotnet, flutter, react, docker, git]
tags: [tipo/sessao, stack/dotnet]
palavras-chave: [heavy, criacao, bootstrap, monorepo, git, push, paralelo, workflow, backend, dominio, sql, rls, redis, azurite]
origem: claude-code
criado: 2026-08-11
atualizado: 2026-09-07
confianca: alta
---

# Criacao do projeto Heavy do zero, em paralelo
## Resumo
Do repositorio vazio ao monorepo com backend .NET, schema Postgres com RLS, infra Docker e push para o GitHub.

## Contexto
11 a 21/08/2026, em `Repos/Pessoal/Heavy`. 162 comandos bash, 3 workflows multi-agente.
Pedido de abertura: *"Vamos comecar a criar o projeto localmente. Depois iremos mandar para o
git. Comece **em paralelo o maximo de tarefas que conseguir**."* → [[trabalhar-em-paralelo]]

## Detalhe

### Sequencia de commits que saiu daqui
```
06afe6a feat(dominio): modelos de evento e ping, e as portas de infraestrutura
2fceb4c feat(sql): schema completo, RLS com FORCE e particionamento de ping
025826d test(integracao): isolamento multi-inquilino contra Postgres real
153daf2 feat(dominio): portas dos servicos de aplicacao e contratos de entrada
26f6c21 feat(aplicacao): relogio, configuracao da operacao e helpers transversais
a33cfed feat(infra): cache Redis, anexos em blob e composicao das infraestruturas
680c13e feat(webapi): composicao, seguranca, middleware de inquilino e ordens
```

Repare na ordem: **dominio → sql → teste de isolamento → aplicacao → infra → webapi**.
De dentro para fora, exatamente como manda [[arquitetura-dotnet-em-camadas]].

### O que ficou de conhecimento
- RLS com `FORCE` e particionamento de ping no schema → [[multi-inquilino-com-rls-no-postgres]]
- Teste de isolamento contra Postgres real, nao mock → [[teste-de-isolamento-que-passa-quebrado]]
- Middleware de inquilino no WebApi (nao no endpoint) → regra 2 do multi-inquilino

### Interrupcao
A sessao bateu no limite de uso e foi retomada com *"Atingi meu limite de uso enquanto voce
trabalhava... continue de onde parou"* — o trabalho seguiu sem perda.

## Relacionado
- [[Heavy]]
- [[multi-inquilino-com-rls-no-postgres]]
- [[arquitetura-dotnet-em-camadas]]
- [[trabalhar-em-paralelo]]
