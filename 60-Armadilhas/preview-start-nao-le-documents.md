---
tipo: armadilha
titulo: preview_start do app Claude nao consegue ler scripts em ~/Documents
projeto: [Rabisco-Hub, todos]
stack: [macos, python]
tags: [tipo/armadilha, stack/macos]
palavras-chave: [preview_start, launch.json, operation not permitted, errno 1, tcc, privacidade macos, documents, servidor local, browser pane, claude desktop]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: media
---

# preview_start nao le scripts em ~/Documents

## Resumo
`preview_start` com `.claude/launch.json` rodando `python3 servir.py` morre com
`can't open file 'servir.py': [Errno 1] Operation not permitted` quando o repo esta em `~/Documents`.

## Contexto
Rabisco hub, 2026-09-20. O processo que o app Claude lanca para o dev server nao tem permissao do
macOS (TCC) para a pasta Documentos; o shell do Bash tem.

## Detalhe
Contorno que funcionou, sem criar `launch.json`:
1. Subir o servidor pelo Bash em segundo plano: `python3 servir.py` (run_in_background).
2. Abrir o painel so com a URL: `preview_start` com `url: http://127.0.0.1:5120/`.
3. No fim, `pkill -f "python3 servir.py"` (o exit 143/144 do job e esse kill, nao erro).

Correcao definitiva seria dar "Acesso total ao disco" ao app Claude nos Ajustes - decisao do Jonas,
nao do agente.

## Relacionado
- [[Rabisco-Hub]]
- [[processo-em-segundo-plano-ignora-sigint]]
- [[testar-html-local-sem-servidor-engana]]
