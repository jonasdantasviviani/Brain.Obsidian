---
tipo: decisao
titulo: "Certificado digital: vale o cadastrado na Focus; o do sistema é opcional"
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next, focus-nfe]
tags: [tipo/decisao, fiscal, certificado, focus-nfe]
palavras-chave: [certificado digital, A1, Focus, prontidão fiscal, arquivo_certificado_base64, Minhas Empresas]
origem: claude-code
criado: 2026-09-30
atualizado: 2026-09-30
confianca: alta
---

# Certificado digital: vale o cadastrado na Focus

## Decisão
Pedido do Jonas: não precisar ter certificado cadastrado no sistema nem na máquina; usar o da Focus sempre
e só cair no cadastro local se a Focus não tiver. Deixar evidente em tela.

## Por quê
- A Focus guarda o A1 (.pfx de eCNPJ/eCPF) cifrado e assina as notas; upload pelo painel (Serviços > Minhas
  Empresas) ou pela API de empresas (`arquivo_certificado_base64`, `senha_certificado`,
  `certificado_especifico`). O sistema só manda JSON com o token da empresa e nunca assina (não há X509
  no código de emissão).
- A API de empresas exige o token principal da conta; o sistema só tem o token por empresa
  ([[focus-token-por-empresa-cifrado]]), então **não dá para perguntar à Focus se o certificado está lá**.

## Como ficou (API#? / Web#?, branch feat/certificado-da-focus)
- `ProntidaoFiscalService`: item "Certificado digital (Focus NFe)" sempre Ok; o cadastro local vira reserva
  opcional e vencido não bloqueia `ProntoNfe`/`ProntoNfse`.
- `NotificacaoService`: alerta de validade passa a falar do cadastro local e vira warning.
- Web: `AvisoCertificadoFocus` (completo no cartão de certificado, compacto na integração Focus e na
  prontidão).

## Em aberto
- Checagem real da validade na Focus exigiria guardar o token principal (decisão de segurança) ou ler o erro
  de certificado nas respostas de emissão.
- "Cair no cadastro local" na prática = enviar o .pfx local à Focus (API de empresas); não implementado.

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
