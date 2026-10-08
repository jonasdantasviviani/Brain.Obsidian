---
tipo: padrao
titulo: Nota de entrada por XML — analisar (não grava) e importar (relê o XML, transação única)
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next, sqlserver]
tags: [tipo/padrao, dominio/fiscal]
palavras-chave: [nota de entrada, xml, nfe, fornecedor, estoque, contas a pagar, Nfe_ProdutoXFornecedor, transação, xxe]
origem: claude-code
criado: 2026-10-06
atualizado: 2026-10-06
confianca: alta
---

## Resumo
Duas etapas: `analisar` lê e confere (sem gravar); `importar` relê o XML e grava tudo em uma transação.

## Detalhe
- Casamento de item: vínculo do código do fornecedor (`Nfe_ProdutoXFornecedor`, aprendido a cada importação) > EAN > código comercial. O que não casa vira cadastro novo preenchido pelo XML, editável na tela.
- Importar relê o XML: o navegador só decide o que o XML não sabe (produto escolhido, fornecedor novo, vencimentos).
- Tabela sem chave não entra pelo EF: INSERT parametrizado (só SQL Server). `AdicionarComCodigoSequencialAsync` precisa participar da transação já aberta.
- Colunas legadas curtas: Produtos.Unidade 3, CodComercial 20, Ncm 8; Debitos.Historico 30; CabEntradas.Usuario 20, Obs 100 — cortar no servidor.
- Bug achado: `Movimentacoes.Sequencia` é IDENTITY; inserir na mão falha no SQL Server (todo estoque quebrado) — usar o gerador de código do repositório.
- Leitor de XML de upload: DTD proibido, sem resolver externo, limite de tamanho, modelo 55 completo.
Relacionado: [[documento-com-itens-no-mesmo-corpo]], [[identity-e-contador-da-tabela-inteira-em-multi-tenant]]. PRs BTech.NFe.Api#96, BTech.Web#72.
