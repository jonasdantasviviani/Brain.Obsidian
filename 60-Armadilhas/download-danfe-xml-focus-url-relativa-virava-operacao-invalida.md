---
tipo: armadilha
titulo: DANFE/XML da Focus davam "Operação inválida." — caminho relativo sem BaseAddress
projeto: [BTech.NFe.Api]
stack: [dotnet, focus-nfe, httpclient]
tags: [tipo/armadilha, stack/dotnet, dominio/fiscal]
palavras-chave: [operação inválida, danfe, xml, caminho_danfe, httpclient, baseaddress, globalexceptionhandler, focus]
origem: claude-code
criado: 2026-10-05
atualizado: 2026-10-05
confianca: alta
---

# DANFE/XML da Focus davam "Operação inválida."

## Sintoma
Depois da nota enviada, ver DANFE, baixar DANFE e baixar/importar XML respondiam 400 "Operação inválida.".

## Causa
A Focus devolve `caminho_danfe` / `caminho_xml_nota_fiscal` **relativos** (`/arquivos/...`, na raiz do host, fora de `/v2`).
`DownloadArquivoAsync` usava `CreateClient()` sem BaseAddress → `InvalidOperationException` do framework. O
`GlobalExceptionHandler` só repassa a mensagem de exceção lançada por código `BTech.*` (regra 15), então virou o texto genérico.

## Correção
Resolver a URL contra o host da Focus antes do GET e mandar o Basic token só se o host for o da Focus (URL assinada de S3 recusa o header).

## Lição
"Operação inválida." genérico = exceção de framework mascarada: procurar o `InvalidOperationException` no log, não na regra de negócio.
Ver [[401-de-servico-externo-repassado-desloga-o-usuario]] e [[BTech.NFe.Api]].
