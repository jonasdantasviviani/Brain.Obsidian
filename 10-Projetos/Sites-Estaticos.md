---
tipo: projeto
titulo: Sites estaticos (landing pages)
projeto: [Sites]
stack: [html, css, javascript]
tags: [tipo/projeto, stack/html, tipo/frontend]
palavras-chave: [landing page, site estatico, html, css, academia, clinica, fotografo, restaurante, agencia, curso, portfolio]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Sites estaticos (landing pages)
## Resumo
Colecao de landing pages em HTML+CSS puro, uma pasta por nicho, sem build e sem framework.

## Contexto
`~/Documents/Repos/Sites/` · sem git nesses diretorios.

## Detalhe

| Pasta | Negocio | Arquivos |
| --- | --- | --- |
| `academia` | ForcaMax Academia | index.html, style.css |
| `agencia` | agencia | index.html, style.css |
| `clinica` | Clinica Vida Plena | index.html, style.css |
| `contato-simples` | pagina de contato | index.html, style.css |
| `fotografo` | Lucas Moraes Fotografo | index.html, selecao.html, style.css, selecao.css, script.js, selecao.js |
| `landing-curso` | curso | index.html, style.css |
| `profissional-liberal` | profissional liberal | index.html, style.css |
| `restaurante` | Cantina da Nonna | index.html, style.css |

### Padrao comum
- `<html lang="pt-BR">`, charset UTF-8, viewport responsivo
- Um unico `style.css` por site, sem preprocessador, sem CDN de framework
- Nav com logo em duas cores (`FORCA<span>MAX</span>`), secoes em rolagem
- `fotografo` e o unico com JS e com uma segunda pagina (`selecao.html` — selecao de fotos)

## Relacionado
- [[Cerebro]]
