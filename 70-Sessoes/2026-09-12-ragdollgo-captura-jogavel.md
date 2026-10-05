---
tipo: sessao
titulo: Ragdoll GO - do zero ate a captura jogavel no navegador
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/sessao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [ragdoll go, pokemon go, captura, bolinha de papel, arremesso, flick, caderno, cilindro, 360 graus, forge2d, metersToPixels, zoom, tela preta, foto de cena, porta 5130, gitignore]
origem: claude-code
criado: 2026-09-12
atualizado: 2026-09-12
confianca: alta
---

# Ragdoll GO - captura jogavel
## Resumo
Jonas pediu para "comecar o jogo do Pokemon GO e testar a captura". O repo `ragdoll-go` so tinha
documento: o codigo foi comecado do zero, reaproveitando o motor do need-for-ragdoll, ate a
captura rodar no navegador em `localhost:5130`.

## Detalhe

### O que foi feito
- `flutter create --org com.rabisco --platforms=web,android,ios --project-name ragdoll_go .`
  dentro do repo (README preservado com backup antes).
- **Copiados do need-for-ragdoll**: tokens, `Caneta` (ganhou `poligono`), `PassoFixo`,
  `MundoFisico`, `Consciencia`, `Osso`, `Junta`, `Ponto`, `Angulo`.
- **Dominio novo**: `Cilindro`; `Especie`/`FichaDaEspecie` (6 bichos, resistencia 1 a 3, peso,
  pontos); `Comportamento` por especie (fuga em rajadas, Sete Pernas tropeca a cada 3 passos,
  Cubo so visivel em janelas de 26 graus a cada 90); `Arremesso` (flick para m/s, 4 a 11);
  `Caca` (derrubou / capturado / ignorado); `Acerto` (proximidade, nao evento de contato);
  `Caderno`; `SorteioDeBicho`; `PoseOsso`.
- **Infra**: esqueletos dos 6 bichos (1 a 5 corpos, juntas com limite), `CorpoBicho`,
  `BolinhaDePapel` (circulo, `isBullet`), `DedoNaTela` traduzindo arrasto em `Comando`.
- **Apresentacao**: `RagdollGoGame` (Forge2DGame), folha com pauta e chao, HUD com moldura de
  caderno e seta, `DesenhoDoBicho` puro, pagina do caderno como overlay.
- **32 testes**: captura, alcance, caderno, cilindro, gesto, comportamento, sorteio e a foto dos
  seis bichos. `flutter analyze` sem nenhum issue.

### Como jogar
- Arrastar para o lado gira o corpo; jogar o dedo (ou o mouse) para cima arremessa.
- Primeira bolinha derruba, o bicho foge rolando em rajadas, a segunda captura (Casinha 1,
  Assinatura 3). Abre a pagina do caderno; "Procurar outro" solta o proximo bicho.
- `flutter build web` + servidor sem cache de [[http-server-do-python-serve-build-velho]] na
  porta **5130**. No celular da mesma rede: `http://<ip-do-mac>:5130`.

### O que custou tempo
1. **Tela preta + `memory access out of bounds`** - `viewfinder.zoom` aplicado por cima de
   `metersToPixels`, ver [[forge2d-viewfinder-zoom-multiplica-meters-to-pixels]]. Parecia Box2D,
   era o CanvasKit.
2. **Porta 5125 ocupada** pelo Counter-Ragdoll: o servidor novo caiu com
   `Address already in use` e o painel mostrou o outro jogo.
3. **Bicho que anda some entre screenshots**: girar pelo painel nunca o pegou em quadro.
   Resolvido fotografando os seis em teste ([[foto-de-cena-em-teste-flutter]]), que achou a
   Espiral em tres bolhas e o rabo do Gato riscado no eixo errado.
4. **`.gitignore` do repo so de documento nao tinha `build/` nem `.dart_tool/`** - o
   `flutter create .` nao mexe em `.gitignore` existente. Bloco Flutter acrescentado.

### O que ficou verificado, e como
- **Por teste**: regra de captura, alcance da bolinha, caderno, cilindro, gesto, comportamento,
  sorteio; desenho dos seis bichos de pe e tombados, por foto.
- **Por olho no painel**: cena renderizando, giro por arrasto (seis vezes), seta do lado certo,
  bolinha arremessada com fisica, console sem erro.
- **Nao verificado**: uma captura completa no navegador (acertar um bicho em quadro) - ficou
  para o Jonas jogar.

### Pendente
- Fundo de camera e bussola (MVP da versao A), caderno salvo entre sessoes.
- Nada foi commitado nesta sessao.

## Relacionado
- [[RagdollGames]]
- [[mundo-cilindrico-com-fisica-2d]]
- [[forge2d-viewfinder-zoom-multiplica-meters-to-pixels]]
- [[foto-de-cena-em-teste-flutter]]
- [[verificar-jogo-no-navegador-do-painel]]
