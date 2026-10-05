---
tipo: seguranca
titulo: 16. Restringir upload de arquivos
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [upload, arquivo, extensao, mime, tamanho, magic number, storage, path traversal, antivirus, webroot, foto, imagem, anexo, documento, canhoto, comprovante, pdf]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 16. Restringir upload de arquivos
## Resumo
Upload valida tipo real, tamanho e nome, e o arquivo nunca fica em pasta servida nem e executado.

## Contexto
Regra 16 das 20 obrigatorias.

## Detalhe

### O checklist minimo
1. **Tamanho maximo** — no endpoint (`RequestSizeLimit`) e no servidor web
2. **Tipo real pelo conteudo**, nao pela extensao nem pelo `Content-Type` (ambos vem do cliente).
   Confira os primeiros bytes (*magic number*): PNG comeca com `89 50 4E 47`
3. **Allowlist de extensao** — nunca blocklist
4. **Renomeie o arquivo** para um GUID no servidor. Nunca use o nome do cliente: `../../etc/passwd`
   e `foto.jpg.exe` sao problema seu
5. **Guarde fora do webroot** — blob storage de preferencia. Se o arquivo nao e servido
   diretamente, metade dos ataques morre
6. **Sirva com `Content-Disposition: attachment`** e `X-Content-Type-Options: nosniff`
   → [[18-security-headers]]
7. **Antivirus** quando o arquivo for compartilhado entre usuarios

### A armadilha do SVG
SVG e XML: aceita `<script>`. Upload de "imagem" que aceita SVG e XSS armazenado.
Ou proiba SVG, ou sanitize, ou sirva de dominio separado.

### No seu codigo hoje
A [[BTech.NFe.Api]] valida tipo, tamanho e extensao — passou na auditoria.
O [[Heavy]] vai receber **foto de canhoto** do app do motorista: quando implementar, este
checklist inteiro se aplica, com storage em blob (Azurite local / Azure Blob em producao).

## Relacionado
- [[14-validacao-dos-inputs]]
- [[18-security-headers]]
