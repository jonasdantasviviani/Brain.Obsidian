---
tipo: armadilha
titulo: str.replace sem assert mente - e o agente relata sucesso de uma edicao que nao aconteceu
projeto: [todos]
stack: [python, claude-code]
tags: [tipo/armadilha, stack/python, cerebro/regra]
palavras-chave: [replace, assert, edicao silenciosa, no-op, heredoc, python, editar arquivo, falso sucesso, verificar edicao]
origem: claude-code
criado: 2026-09-23
atualizado: 2026-09-23
confianca: alta
---

# replace sem assert mente

## Resumo
`t = t.replace(velho, novo)` com `velho` que nao existe mais **nao levanta nada**: devolve o
texto igual, o script grava o arquivo sem mudanca e imprime o "ok" do fim. O agente entao
**relata ao usuario uma edicao que nunca aconteceu**.

## Contexto
2026-09-23, vault do Jonas. Eu marquei um documento de decisao como "DECIDIDO PELO DONO",
imprimi "documento marcado como decidido" e contei isso a ele. O documento continuava dizendo
"EM ABERTO": uma edicao anterior tinha quebrado a linha do cabecalho, e o meu `velho` casava
com a versao de uma linha so. So descobri porque reli o arquivo por outro motivo.

## Detalhe
A causa raiz e sempre a mesma: **edicoes encadeadas no mesmo arquivo**. A segunda edicao casa
contra o texto que a primeira deixou, nao contra o que voce leu. Quebra de linha, espaco duplo
e acento reescrito sao suficientes.

### A regra
```python
assert velho in t, "trecho nao encontrado"   # antes de todo replace
t = t.replace(velho, novo, 1)                # e sempre com contagem
p.write_text(t, encoding="utf-8")
print(p.read_text(encoding="utf-8")[:400])   # e leia de volta o que gravou
```
- **Sempre `assert ... in ...`** antes do `replace`. Sem isso o script nao tem como falhar.
- **Sempre `, 1`**: sem contagem, um trecho repetido e trocado em todo lugar, calado.
- **Reler e imprimir** o pedaco editado: o "ok" do fim do script nao prova nada.
- Com a ferramenta `Edit` isso vem de graca (ela erra quando nao casa). O risco e do
  `python3 - <<EOF` com `replace`, que e o caminho comum para editar varios arquivos de uma vez.

### Por que importa mais para um agente
Um humano abre o arquivo e ve. O agente reporta o print do proprio script - e o print mente
junto. Vira informacao errada no chat **e** no vault.

## Relacionado
- [[cadeia-com-pipe-esconde-falha-de-build]]
- [[Protocolo-Cerebro]]
