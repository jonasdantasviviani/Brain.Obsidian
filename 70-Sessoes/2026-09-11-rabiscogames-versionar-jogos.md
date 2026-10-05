---
tipo: sessao
titulo: RabiscoGames - preparar os jogos para versionar na organizacao
projeto: [RagdollGames]
stack: [git, github]
tags: [tipo/sessao, stack/git, dominio/jogos]
palavras-chave: [github, organizacao, RabiscoGames, repositorio privado, gitignore, segredos, roteiro, docs, gh auth]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# RabiscoGames - preparar os jogos para versionar
## Resumo
Jonas criou a organizacao **github.com/RabiscoGames** para versionar os jogos, **um repositorio
por jogo**. Decisoes dele: repositorios **privados**; o **roteiro de cada jogo vai para `docs/`**
do proprio repositorio; catalogo, identidade e kit de producao da serie ficam locais.

## Detalhe
- Repositorios: `counter-ragdoll` e `need-for-ragdoll` (cada um ja era git proprio, branch
  `main`). A pasta `Repos/Games` nao e repositorio e continua assim.
- **Auditoria antes de publicar** (regra 02): nenhum segredo, nada de `build/`, um autor so.
  O `.gitignore` nao cobria `.env` nem chave de assinatura - adicionado bloco de segredos
  (`.env*`, `*.jks`, `*.keystore`, `key.properties`, `*.p12`, `*.pem`, `*.mobileprovision`,
  `google-services.json`, `GoogleService-Info.plist`).
- **Roteiro copiado** para `docs/roteiro.md`: links `../comum/*.md` viraram texto
  *(serie: arquivo)*, porque no GitHub seriam links quebrados. O original em
  `ragdoll-games/jogos/` ganhou aviso apontando para a **versao viva** no repo do jogo.
- **Tropeco:** o aviso entrou dentro do frontmatter YAML (inseri "depois da primeira linha", que
  e o `---`). Corrigido antes de publicar. Em nota com frontmatter, inserir **depois do titulo H1**.
- **Bloqueio resolvido:** `gh` nao estava autenticado (criar repo exige a API; push por
  keychain nao cria repo). Jonas rodou `gh auth login` (conta `jonasdantasviviani`) e os repos
  foram criados com `gh repo create RabiscoGames/<jogo> --private --description ... --source .
  --remote origin --push`.
- **Publicado e conferido no GitHub:** os dois PRIVATE, branch `main` rastreando `origin/main`,
  mesmo commit no topo local e remoto (counter-ragdoll 15 commits, need-for-ragdoll 3),
  `docs/roteiro.md` presente nos dois.

### Segunda parte - todos os jogos com roteiro
Jonas pediu repo para **todas** as ideias, das franquias e as pessoais, com roteiro e MDs.
- **9 repos novos** (privados): `grand-thief-ragdoll`, `doodlecraft`, `ragdoll-go` (Ragdoll) e
  `frota`, `cozinha-infinita`, `deep-miner`, `torre-de-cartas`, `jardim-de-automatos`,
  `um-botao` (Progressivos). Pastas locais novas em `Repos/Games/<repo>/`, so com documentos.
- Cada um: README gerado do **Pitch** do roteiro, `docs/roteiro.md`, documentos da linha em
  `docs/serie/` ou `docs/progressivos/`, `.gitignore` com o bloco de segredos.
- `counter-ragdoll` e `need-for-ragdoll` ganharam a mesma estrutura (os links que eu tinha
  virado texto voltaram a ser links, agora para `docs/serie/`).
- **Conferido:** 432 links relativos, 0 quebrados; todo link externo aponta para um dos 11 repos;
  os 11 PRIVATE e com o mesmo commit local e remoto.
- As ~55 franquias do catalogo **sem roteiro** nao ganharam repo - "com seus roteiros" pede
  roteiro. Ver [[um-repo-por-jogo-com-docs-da-linha]].

## Relacionado
- [[RagdollGames]]
- [[github-cli-gh]]
- [[02-limpar-secrets-do-git]]
