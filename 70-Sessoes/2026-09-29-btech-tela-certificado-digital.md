---
tipo: sessao
titulo: "BTech: tela de certificado digital com envio validado do .pfx"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next, focus-nfe]
tags: [tipo/sessao, fiscal, seguranca, certificado]
palavras-chave: [certificado digital, pfx, senha, cnpj, aes-gcm, GerenciarCertificado, focus empresas]
origem: claude-code
criado: 2026-09-29
atualizado: 2026-09-29
confianca: alta
---

# BTech: tela de certificado digital

## Resumo
PRs API #55 e Web #37 (branch `feat/tela-certificado-digital`). Envio multipart do .pfx com senha,
titular/CNPJ/validade lidos de dentro do arquivo, .pfx cifrado e senha descartada.

## Achados
- O sistema não assina com o certificado (sem X509 no código); `Certificado` é só cadastro e validade.
  Antes o `PfxBase64` ficava em claro e a validade vinha do cliente.
- Focus tem `POST/PUT /v2/empresas` com certificado, mas exige o token principal da conta, não o por
  empresa — envio à Focus ficou de fora (ver [[focus-token-por-empresa-cifrado]]).
- CNPJ do certificado: raiz de 8 dígitos diferente recusa; filial com certificado da matriz só avisa.
- Cifrador exige `Criptografia:Chave` ([[05-criptografia-de-dados-sensiveis]], [[16-restringir-upload-de-arquivos]]).

## Armadilhas
- `Unit` na main não compila (`NfseEmissaoServiceMontarTests` sem `httpClientFactory`).
- CNPJ de teste precisa de dígito verificador válido (POST /api/empresas valida).
- macOS: `X509KeyStorageFlags.EphemeralKeySet` não funciona ao carregar PFX; usar DefaultKeySet.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
