---
tipo: armadilha
titulo: Processo lancado com & por shell nao interativo ignora SIGINT - kill -INT nao testa Ctrl+C
projeto: [Rabisco-Hub, todos]
stack: [bash, zsh, python]
tags: [tipo/armadilha, stack/bash, stack/python]
palavras-chave: [sigint, ctrl+c, kill -INT, segundo plano, background, &, keyboardinterrupt, sig_ign, sig_dfl, preexec_fn, send_signal, servidor preso na porta, teste de sinal]
origem: claude-code
criado: 2026-09-13
atualizado: 2026-09-13
confianca: alta
---

# Processo em segundo plano ignora SIGINT

## Resumo
`python3 servir.py 5199 &` seguido de `kill -INT $PID` **nao para o servidor**: ele continua
escutando na porta. Parece que o programa nao trata Ctrl+C, mas o sinal nem chegou.

## Contexto
[[Rabisco-Hub]], 2026-09-13, testando o Ctrl+C do `servir.py` pela ferramenta Bash antes de
documentar no README. O servidor de teste ficou preso na porta 5199 ate um `kill` normal.

## Detalhe

### Por que
- POSIX: shell **sem controle de job** (o caso de script e da ferramenta Bash) roda o comando com `&`
  com SIGINT e SIGQUIT **ignorados**.
- Sinal ignorado continua ignorado depois do `exec`.
- O Python so transforma SIGINT em `KeyboardInterrupt` "se o processo pai nao mudou o sinal". Chegou
  ignorado, fica ignorado.

No terminal de verdade o Ctrl+C vai para o processo em primeiro plano, que nao tem esse problema.

### Como testar Ctrl+C de verdade
Lancar o filho com o SIGINT de volta ao padrao e mandar o sinal pelo Python:
```python
import signal, subprocess, sys
p = subprocess.Popen([sys.executable, "servir.py"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT,
                     text=True, preexec_fn=lambda: signal.signal(signal.SIGINT, signal.SIG_DFL))
# ... le a saida ate o servidor subir, faz as requisicoes ...
p.send_signal(signal.SIGINT)
saida, _ = p.communicate(timeout=10)   # p.returncode e a saida real do Ctrl+C
```
Para so limpar um servidor de teste esquecido: `kill <pid>` (TERM) depois de conferir o comando com
`ps -p <pid> -o command=`.

### Tropeco junto
No mesmo teste, `python3 servir.py 5199 2>&1 | tail -4; echo "saida=$?"` mostrou `saida=0` com o
traceback de porta ocupada na tela: o `$?` era do `tail`. Ver
[[cadeia-com-pipe-esconde-falha-de-build]].

## Relacionado
- [[Rabisco-Hub]]
- [[cadeia-com-pipe-esconde-falha-de-build]]
- [[bash-paralelo-compartilha-diretorio]]
- [[2026-09-13-rabisco-hub-readme-como-iniciar]]
