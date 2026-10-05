---
tipo: sessao
titulo: DTOs de Cliente, Empresa e Usuário + máscara de CPF
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, aspnetcore, next, typescript]
tags: [tipo/sessao, seguranca]
palavras-chave: [dto, mass assignment, hash de senha, vazamento, mascara cpf, documento-existe, 08, 17, 05]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# DTOs de Cliente, Empresa e Usuário + máscara de CPF
## Resumo
PRs: API #59 e Web #41 (branch `sec/dtos-cliente-empresa-usuario`). Regras [[08-bloquear-mass-assignment]], [[17-trim-nas-respostas-de-api]], [[05-criptografar-dados-sensiveis]].

## Detalhe
- **Achado grave:** `GET /api/usuarios` devolvia a entidade `Formula` com o hash BCrypt da senha de todos os usuários; `Empresa` expunha `ConexaoMsFrenteCaixa`. Corrigido com `UsuarioResponse`/`EmpresaResponse`.
- Padrão: `*Response` + `*CriarRequest` (pasta `WebApi/Dtos`), mapeamento manual (`De`), `ValidarEntidadeAsync` porque o AutoValidation não valida entidade montada no controller, `AtualizarParcialComRespostaAsync` mantém o PUT parcial. Ver [[put-parcial-sobre-o-registro-existente]].
- CPF de PF mascarado (`***.456.789-**`) só na listagem; busca por dígitos segue no servidor; `GET /api/clientes/documento-existe` substitui a checagem em `fetchAllPages` no Web.
- Armadilhas: o Web lia campos que a API nunca teve (`telefone`, `cnpj`, `idEmpresa`, `data` na lista de usuários) — ao trocar contrato, comparar com o que a entidade realmente serializa. Testes com CPF fixo repetido em teste compartilhado (InMemory) quebram `Single`/duplicidade: use um CPF por teste. Erro meu: máscara esperada calculada de cabeça (`digits[3..6]`), conferir.
- Na main a suíte Unit não compilava (`NfseEmissaoServiceMontarTests`, PR #54 de outro agente).
- Falta: Fornecedor (mesma máscara), demais ~24 controllers (roteiro no CLAUDE.md da API).
