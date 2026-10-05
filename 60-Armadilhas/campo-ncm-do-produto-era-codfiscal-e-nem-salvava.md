---
tipo: armadilha
titulo: Tela de Produto rotulava "NCM" um campo que era CodFiscal — e nem salvava
projeto: [BTech.Web]
stack: [next, react, dotnet]
tags: [tipo/armadilha, stack/next, dominio/fiscal]
palavras-chave: [ncm, codfiscal, produtos, rotulo errado, campo nao enviado, tofpayload, perda silenciosa de dado]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-28
confianca: media
---

# Tela de Produto rotulava "NCM" um campo que era CodFiscal — e nem salvava
> **CORREÇÃO (2026-09-28):** a conclusão abaixo de que "NCM não existia em lugar nenhum" está
> **errada**. Olhando os DADOS da base real (não só o schema): `Produtos.CodFiscal` **é o NCM**
> ("63079010"), `ClFiscal` é a tabela de NCMs (chave = o NCM) e `CorNotas.ClFiscal` é o NCM do
> item. O rótulo antigo "NCM" no campo CodFiscal estava certo; o bug real era só o campo não ir no
> payload. Ver [[conclusao-tirada-do-schema-sem-olhar-os-dados]].

## Resumo
`produtos/novo` e `produtos/[id]/editar` tinham um campo com `<Label>NCM</Label>` que na verdade
estava ligado a `form.codFiscal` (classificação fiscal / ClFiscal, um conceito diferente de NCM).
Pior: `toPayload()` nem incluía `codFiscal` no corpo enviado — o usuário digitava algo ali,
salvava, e o valor nunca chegava no backend. NCM (Nomenclatura Comum do Mercosul) não existia em
lugar nenhum do schema — nem em `Produto`, nem em `CorNota`, nem em `ClFiscal`.

## Contexto
Descoberto em 2026-09-16 ao montar a ponte real de emissão de NF-e (a Focus exige `codigo_ncm`
obrigatório por item — ver [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]]).
Ao procurar de onde viria o NCM de cada produto para o mapper, a busca por "Ncm" em toda a
entidade `Produto.cs` deu zero resultado — e ao abrir a tela real de cadastro pra confirmar,
achei o campo rotulado "NCM" apontando pro campo errado.

## Detalhe
Corrigido dos dois lados:
- Backend: nova coluna `Produtos.Ncm` (migration `017_produto_ncm.sql`) + `Produto.Ncm` na
  entidade — ver [[BTech.NFe.Api]].
- Frontend: label do campo antigo trocado para "Código Fiscal (Cl. Fiscal)" (mantendo o bind em
  `codFiscal`, que passou a ir no payload também — outro bug do mesmo lugar), e um campo novo de
  verdade "NCM \*" ligado a `form.ncm`, em `produtos/novo/page.tsx` e `produtos/[id]/editar/
  page.tsx`.

Mesma família de bug que [[importador-casava-coluna-pelo-nome-da-propriedade]] e
[[selects-mostrando-valor-bruto]]: um campo de formulário existia visualmente e passava a
impressão de estar funcionando, mas o dado nunca completava o caminho até o banco. Vale conferir
outras telas de cadastro fiscal (Fornecedores, Clientes) por esse mesmo padrão — campo na tela que
não está em `toPayload()` — não foi feita uma varredura completa disso ainda.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[focus-nfe-nomes-de-campo-tinham-sido-inventados-sem-checar-doc]]
- [[importador-casava-coluna-pelo-nome-da-propriedade]]
- [[selects-mostrando-valor-bruto]]
