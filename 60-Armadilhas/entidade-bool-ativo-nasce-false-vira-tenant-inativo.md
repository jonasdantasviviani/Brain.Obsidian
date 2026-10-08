---
tipo: armadilha
titulo: Entidade com bool Ativo nasce false e vira "inativo" ao ligar uma checagem nova
projeto: BTech.NFe.Api
stack: [dotnet, efcore, testes]
tags: [multi-tenant, middleware, testes]
palavras-chave: [Tenant.Ativo, AcessoMiddleware, login 401, factory de teste, cache de acesso, consultas por requisição]
origem: sessao 2026-10-06 admin tenant/perfil
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---
# Bool `Ativo` sem default nasce `false`

- `Tenant.Ativo` era `bool` sem inicializador. Ao passar a recusar tenant inativo (login + `AcessoMiddleware`), TODAS as fábricas de teste que faziam `new Tenant { Nome, Token }` viraram "inativas": login 401 e centenas de falhas em Contract/Functional. Correção: `= true` na entidade (e é o certo para tenant criado pelo painel).
- Middleware que consulta tenant/perfil por requisição aumenta a contagem de queries dos testes de carga (`ConsultasPorRequest`): aquecer o cache (`GET /health` autenticado) no helper de login.
- Teste de ref da Focus ainda procurava `-{id}-` depois que a `ref` virou a `Sequencia` (ver [[BTech.NFe.Api]]).
- Regra do produto: perfil define telas; sem perfil/perfil vazio = sem limite. Ver [[BTech.NFe.Api]].
