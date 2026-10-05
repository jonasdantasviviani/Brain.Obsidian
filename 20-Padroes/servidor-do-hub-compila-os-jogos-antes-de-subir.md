---
tipo: padrao
titulo: O servidor do hub compila os jogos antes de subir, e anota na pagina quem esta pronto
projeto: [Rabisco-Hub, RagdollGames]
stack: [python, flutter, html]
tags: [tipo/padrao, stack/python, dominio/jogos]
palavras-chave: [servir.py, barra de progresso, etapas numeradas, isatty, linha viva, killpg EPERM, vigia de cpu, build antes de subir, build velho, mtime, anotacao local, bilhete, injetar html no servidor, csp style-src, sem-build, flutter nao responde, vitrine mente]
origem: claude-code
criado: 2026-09-22
atualizado: 2026-09-22
confianca: alta
---

# O servidor local compila os jogos antes de subir

## Resumo
Uma vitrine que diz "jogavel" e nao abre e pior que uma vitrine vazia: o `servir.py` compila o que
esta faltando **antes** de servir e escreve na propria pagina quais jogos estao prontos aqui.

## Contexto
Jonas, 2026-09-22: "o jogo tem q estar buildado e executando, nao consigo rodar por exemplo o need
for ragdoll. Ele deve tentar buildar os jogos antes de abrir o hub". O cartao dizia "Protótipo
jogável" enquanto `../need-for-ragdoll/build/web` nem existia.

## Detalhe

### As quatro regras
1. **Compilar so o que precisa**: `build/web/index.html` mais velho que qualquer arquivo de
   `lib/`, `web/`, `assets/`, `pubspec.yaml` ou `pubspec.lock` = build velho. Em dia nao recompila,
   entao a segunda subida e instantanea.
2. **Provar o compilador antes de usar** (`flutter --version`, 60 s): com o SDK evictado pelo
   iCloud o build fica pendurado com 0% de CPU - sem a prova, 3 jogos x 15 min de espera por nada.
   Ver [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]].
3. **Falha de build nunca derruba o hub**: sobe assim mesmo, mostra as 8 ultimas linhas do erro e
   marca o jogo como sem build. Se o comando nao escreveu **nenhuma** linha, dizer isso e apontar
   disco/iCloud - e o sintoma classico.
4. **Conferir a porta antes de compilar**: descobrir a porta ocupada depois de 5 min de compilacao
   irrita. `socket.bind` + fechar, depois compila, depois sobe de verdade.

### A anotacao local
O paragrafo `<p class="local">` e **injetado na hora** pelo servidor (`html.replace("<body>", ...)`),
nunca gravado no `site/index.html` versionado: no ar "esta rodando nesta maquina" nao quer dizer
nada. Como a CSP do hub e `style-src 'self'`, **nao da para usar `style=` inline**: a regra `.local`
mora no `estilo.css` que ja vai ao ar (poucas linhas ociosas la, zero gambiarra aqui).

```python
def index_do_hub(self):
    html = (SITE / "index.html").read_text(encoding="utf-8")
    return self.pagina(html.replace("<body>", "<body>\n  " + anotacao_local(), 1))
```

O texto sai em portugues de gente: "Neste servidor: Counter-Ragdoll, Need for Ragdoll e Ragdoll GO
estao buildados e rodando - e so clicar em Jogar." Faltando algum: "Ainda sem build, nao abre: X -
o terminal diz por que."

### O terminal: quatro etapas e uma barra
```text
[3/4] Build dos jogos
      counter-ragdoll: build em dia
      [############......] 2/3  need-for-ragdoll: compilando 1m58s
```
- `Terminal.etapa()` numera (`[n/4]`), `andamento()` escreve a **linha viva** (`\r\x1b[2K`) e
  `dito()` deixa a linha definitiva por cima dela.
- **`sys.stdout.isatty()` decide**: fora de um terminal (log, pipe, launchd) nada e reescrito -
  senao o arquivo enche de `\r`. Uma linha por acontecimento e pronto.
- A barra anda por jogo; dentro de um build quem se mexe e o relogio (a cada 1 s), e passar
  10 s sem gastar CPU ja aparece na propria linha antes de o vigia desistir.
- Testar barra de terminal sem terminal: `script` do macOS reclama (`tcgetattr/ioctl`), mas
  `pty.openpty()` + `subprocess.Popen(stdout=escravo)` entrega um TTY de verdade ao filho.

### killpg pode dar EPERM - e ai o servidor caia
O vigia matava o build com `os.killpg`, que levanta **PermissionError** quando o sistema nao
deixa (aconteceu no teste). Sem `except OSError` isso derrubava o `servir.py` inteiro **no
lugar de** so desistir daquele build. E `processo.wait()` depois de um kill que falhou espera
para sempre: use `wait(timeout=10)` e siga a vida, avisando o pid.

### Escape valve
`python3 servir.py --sem-build` sobe na hora com o que houver no disco. E o que se usa quando a
maquina esta ruim e voce so quer olhar o catalogo.

## Relacionado
- [[Rabisco-Hub]]
- [[hub-de-jogos-web-um-caminho-por-jogo]]
- [[icloud-evicta-o-sdk-do-flutter-e-trava-o-dart]]
- [[cadeia-com-pipe-esconde-falha-de-build]]
- [[2026-09-22-rabisco-hub-need-for-ragdoll-jogavel]]
