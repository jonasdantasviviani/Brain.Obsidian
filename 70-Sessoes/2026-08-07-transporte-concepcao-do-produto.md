---
tipo: sessao
titulo: Concepcao do produto Transporte (que virou Heavy Ops)
projeto: [Heavy]
stack: [produto, mercado]
tags: [tipo/sessao, stack/produto]
palavras-chave: [transporte, heavy, produto, product owner, analise de mercado, concorrentes, mvp, backlog, especificacao, bff]
origem: claude-code
criado: 2026-08-07
atualizado: 2026-09-07
confianca: alta
---

# Concepcao do produto Transporte (que virou Heavy Ops)
## Resumo
Sessao longa de product owner + analista de mercado que gerou os ~29 documentos de especificacao do [[Heavy]].

## Contexto
07 a 11/08/2026, em `Repos/Pessoal/Transporte` — pasta depois renomeada para `Heavy`.
99 edicoes, 34 arquivos escritos, 37 arquivos tocados, 21 tarefas criadas.

## Detalhe

### O que foi pedido
1. "Aja como um product owner senior e avalie a ideia... Aja tambem como um analista de mercado
   senior e valide concorrentes, mercado"
2. "Quero ter mapeado **todas** as ideias mesmo que nao entrem no MVP. Sugira melhorias ou coisas
   que nao pensei"
3. "Faca mais arquivos, **quanto mais detalhado a gente pensar mais rapido vai ser o
   desenvolvimento**"

### O que saiu
Os documentos `00-*.md` a `28-*.md` da raiz do Heavy, que ate hoje sao a **fonte da verdade** do
projeto: atores, nucleo ordem/jornada, modulos, rastreamento, frota, importacao, arquitetura
tecnica, modelo de negocio, backlog, concorrentes, riscos, banco de ideias, maquina de estados,
contratos de API, telas, stack, seguranca, qualidade, glossario, arquitetura .NET, app Flutter.

### Decisoes de stack tomadas aqui
- "Vai ser o backend em .net 10" → [[dotnet-10-como-padrao-de-backend]]
- "o backend vai ser em c# com nuvem, o app vai ser em flutter e o painel administrativo em react"

### Pergunta que ficou registrada
> "acha que faz sentido quebrar a API? Por exemplo, penso em ter a API backend com o core do
> negocio, ai teria um BFF para o app e um BFF para o portal."

O desenho final nao criou projetos BFF separados: virou **um documento OpenAPI por grupo de
consumidores** (`integracao`, `app`, `painel`) dentro da mesma API.
Ver [[openapi-gerado-do-codigo]] e [[monolito-modular-em-vez-de-microservicos]].

## Relacionado
- [[Heavy]]
- [[dotnet-10-como-padrao-de-backend]]
- [[openapi-gerado-do-codigo]]
