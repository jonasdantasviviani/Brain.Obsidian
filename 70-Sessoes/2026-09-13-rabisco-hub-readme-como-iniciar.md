---
tipo: sessao
titulo: Rabisco Games hub - README de como iniciar e servir.py sem traceback
projeto: [Rabisco-Hub, RagdollGames]
stack: [python, git]
tags: [tipo/sessao, dominio/jogos]
palavras-chave: [readme, como iniciar, servir.py, porta ocupada, errno 48, eaddrinuse, ctrl+c, keyboardinterrupt, hub parado, clone do zero, gh repo clone, build web, porta 5120]
origem: claude-code
criado: 2026-09-13
atualizado: 2026-09-13
confianca: alta
---

# Rabisco Games hub - README de como iniciar

## Resumo
Jonas: "deixe um readme de como iniciar o hub". O README ja tinha "Rodar local" no meio; virou a
secao **"Como iniciar"** no topo, e o `servir.py` deixou de mostrar traceback nos dois casos que o
README teria de explicar.

## Detalhe
- **README** (`Repos/Games/hub/README.md`), antes da referencia que ja existia:
  1. conferir `python3 --version` (3.9+, nada para instalar)
  2. `cd ~/Documents/Repos/Games/hub && python3 servir.py`, com a saida esperada
  3. abrir `http://127.0.0.1:5120`
  4. parar com Ctrl+C
  - Jogar pelo hub: build em `../<slug>/build/web/`, `flutter build web --release`, sem reiniciar
  - Clone do zero: `gh repo clone RabiscoGames/hub` + os jogos ao lado, sem `gerar.py`
  - Depois de mexer no catalogo: `gerar.py`; reiniciar so com jogo novo ou `slug` mudado
  - Tabela de problemas comuns (porta ocupada, "ainda nao tem build", JSON sem efeito, duplo clique
    no `index.html`, celular)
- **servir.py**: `errno.EADDRINUSE` -> mensagem com `lsof -nP -iTCP:<porta> -sTCP:LISTEN` e sugestao
  de `porta + 20`, sai 1; porta nao numerica ou fora de 1-65535 -> uso, sai 2; `KeyboardInterrupt` ->
  `Hub parado.`, sai 0; `sys.exit(main())`.
- Commit e push na `main` de `RabiscoGames/hub`.

### Verificacao
- Clone limpo em pasta temporaria: `site/index.html` e `site/filtros.css` presentes, `gerar.py
  --conferir` 0, servidor sobe com "(0 de 84 jogos com build)", `/ragdoll-go/` 404 "ainda nao tem
  build", e depois de criar `../ragdoll-go/build/web/index.html` com o servidor rodando, 200 com
  `<base href="/ragdoll-go/">` - sem reiniciar.
- `servir.py` novo por `subprocess` com SIGINT padrao: saida ao subir identica ao bloco do README,
  segundo servidor na 5120 sai 1 com a mensagem, `abc` e `70000` saem 2, SIGINT sai 0 com
  `Hub parado.` e libera a porta.

### Tropeco
`kill -INT` em processo lancado com `&` nao chega - ver
[[processo-em-segundo-plano-ignora-sigint]]. O servidor de teste ficou preso na 5199.

## Relacionado
- [[Rabisco-Hub]]
- [[processo-em-segundo-plano-ignora-sigint]]
- [[2026-09-12-rabisco-hub-catalogo-e-git]]
