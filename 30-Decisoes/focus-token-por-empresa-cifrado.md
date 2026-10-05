---
tipo: decisao
titulo: "Token da Focus NFe por empresa e por ambiente, cifrado em tabela própria"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, sqlserver, focus-nfe, next]
tags: [tipo/decisao, fiscal, seguranca]
palavras-chave: [focus nfe, token, empresa, ambiente, homologacao, producao, EmpresasFocus, AES-GCM, CRIPTOGRAFIA_CHAVE, certificado digital]
origem: claude-code
criado: 2026-09-26
atualizado: 2026-09-26
confianca: alta
---

# Token da Focus NFe por empresa e por ambiente, cifrado em tabela própria

## Resumo
Token da Focus fica por empresa e ambiente em `EmpresasFocus`, cifrado com AES-GCM e nunca devolvido pela API.

## Contexto
O Jonas: "o token focus é por empresa e por ambiente". A API tinha um `FocusNfe:Token` global —
só serve para uma empresa. O certificado digital fica na Focus (tela de certificado removida).

## Decisão
- Tabela `EmpresasFocus` (migração 021, PK IdTenant + CodEmpresa), **não** colunas em `Empresas`:
  a entidade Empresa vai inteira no GET e é gravada pelo PUT genérico — o token vazaria.
- AES-GCM com chave de `Criptografia:Chave` (env `CRIPTOGRAFIA_CHAVE`, scripts de setup geram).
  Descartado `IDataProtector`: chaves no disco do container, somem ao recriar sem volume.
- Token nunca sai da API (só `temToken` + 4 últimos). PUT/testar exigem super usuário ou admin
  da empresa (`ConfigurarIntegracaoFiscal`).
- `IFocusNfeService.ComCredencial(token, ambiente)`; `IEmpresaFocusService` resolve por empresa,
  global vira fallback. Webhook resolve pela nota.
- Testar token: consultar ref inexistente — 404 = token aceito, 401 = recusado.

## Pendente
NFC-e, NFS-e e Manifesto ainda usam o token global. Trocar a chave obriga a recadastrar tokens.
Ver [[401-de-servico-externo-repassado-desloga-o-usuario]].

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[401-de-servico-externo-repassado-desloga-o-usuario]]
- [[focus-ignora-em-silencio-campo-que-nao-reconhece]]
- [[2026-09-26-btech-emissao-fiscal-e-token-focus-por-empresa]]
