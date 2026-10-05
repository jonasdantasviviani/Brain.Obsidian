---
tipo: armadilha
titulo: "Tela de financeiro lia campos que a API nunca mandou (camelCase de Ndocumento/Codfor)"
projeto: [BTech.Web, BTech.NFe.Api]
stack: [dotnet, next, typescript]
tags: [tipo/armadilha, contrato-de-api, json, dados-legados]
palavras-chave: [JsonNamingPolicy, camelCase, Ndocumento, ndocumento, CodEmpresa, Codfor, interface TypeScript, coluna em branco, financeiro, debitos, creditos]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Tela de financeiro lia campos que a API nunca mandou

## Resumo
A interface `Debito` do frontend declarava `idEmpresa`, `codFor` e `nDocumento`. A entidade do
backend tem `CodEmpresa`, `Codfor` e `Ndocumento`, que com `JsonNamingPolicy.CamelCase` viram
`codEmpresa`, `codfor` e `ndocumento`. Três das seis colunas da tabela liam `undefined`.

A regra do camelCase do .NET só rebaixa a primeira letra (e a corrida inicial de maiúsculas):
`Ndocumento` → `ndocumento`, **não** `nDocumento`. Quem lê o nome escalfoldado em PascalCase
converte de cabeça para o camelCase "bonito" e erra.

## Por que não deu erro
TypeScript não valida JSON em tempo de execução. Uma interface é uma promessa, não uma checagem —
campo ausente vira `undefined` e a célula renderiza vazia.

## Somado a outro problema
A tela só mostrava **contas a pagar** (`Debitos`). Cliente cujo movimento é quase todo a receber
(`Creditos`) via a tela completamente vazia — "não há nada na página de financeiro". Duas causas
independentes com o mesmo sintoma.

## Lição
Interface TypeScript escrita "de cabeça" a partir do nome da entidade C# é palpite. Confira contra
a resposta real (ou contra a regra do `JsonNamingPolicy`) — principalmente em schema legado, onde
os nomes não seguem convenção nenhuma.

Relacionado: [[base-legada-btech-delphi]], [[entidade-sem-colecao-descarta-itens-no-binder]].
