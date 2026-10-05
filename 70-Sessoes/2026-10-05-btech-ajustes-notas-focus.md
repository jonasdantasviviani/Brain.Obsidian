---
tipo: sessao
titulo: BTech — DANFE/XML, devolução, serviços na nota de produto, ref = Sequencia
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next]
tags: [tipo/sessao]
palavras-chave: [danfe, devolução, nfse, ref, focus]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

# Sessão 2026-10-05 — BTech

- DANFE/XML: [[download-danfe-xml-focus-url-relativa-virava-operacao-invalida]].
- Devolução: [[cfop-de-entrada-em-nota-de-saida-na-devolucao]] (+ explicações de CFOP na tela).
- Serviços na mesma nota: `POST /api/nfse/notas/de-servicos` (`NfseDeServicosService`) + seção "Serviços prestados" em `EditorNotaFiscal`
  (nota nova de produto); seletor de tipo não oferece mais "Serviço". NF-e e NFS-e são salvas/emitidas juntas, sem vínculo no banco.
- Ref: [[ref-focus-igual-sequencia-da-nota-e-busca-por-tenant]].
- Testes: 5 falhas pré-existentes em `NfseEnvioMutacaoTests` (não são desta sessão). FocusFake (precisa SQL) não foi rodado — só compilado.
