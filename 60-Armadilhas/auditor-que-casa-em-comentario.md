---
tipo: armadilha
titulo: Auditor de codigo que casa em comentario da falso positivo
projeto: [Cerebro]
stack: [python, seguranca]
tags: [tipo/armadilha, stack/python, seguranca, cerebro/critico]
palavras-chave: [auditor, grep, regex, comentario, falso positivo, evidencia, scanner, analise estatica, documentacao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Auditor de codigo que casa em comentario da falso positivo
## Resumo
Buscar evidencia de seguranca com regex sobre o arquivo inteiro conta comentario como implementacao — e comentario e exatamente onde seguranca e discutida.

## Contexto
07/09/2026. Depois de corrigir as regras 21-28, o auditor deu **tres "ok" falsos** de uma vez.

## Detalhe

### Como apareceu
Eu tinha acabado de escrever, em comentario:

```csharp
/// ...a Focus NFe nao assina o webhook... sem HMAC, sem header de assinatura.
// ...Sem revogacao, cada minuto extra conta.
/// <summary>Soft delete... auditoria e historico dependem dele.</summary>
```

E o auditor respondeu:
- regra 24: "assinatura verificada" (nao ha HMAC nenhum)
- regra 25: "sessao com revogacao" (nao ha revogacao nenhuma)
- regra 26: "ha estrutura de trilha de auditoria" (nao ha)

Os tres vieram de **comentario**, nenhum de codigo.

### Por que e pior do que parece
O vies e sistematico e vai **sempre na direcao errada**: quanto melhor documentado o motivo de
uma protecao **nao** existir, mais o auditor acredita que ela existe. Um projeto que escreve
"TODO: falta rate limit" passa na checagem de rate limit.

E o falso positivo e mais caro que o falso negativo: um "falta" errado custa cinco minutos de
conferencia; um "ok" errado custa a protecao.

### A correcao
Remover linhas que sao **so** comentario antes de qualquer busca:

```python
COMENTARIO_LINHA = {".cs": r"^\s*(//|///|\*|/\*)", ".sql": r"^\s*(--|/\*)", ".py": r"^\s*#", ...}

def sem_comentarios(texto, rel):
    padrao = COMENTARIO_LINHA.get(os.path.splitext(rel)[1])
    if not padrao:
        return texto
    rx = re.compile(padrao)
    return "\n".join(l for l in texto.splitlines() if not rx.match(l))
```

So linhas que **comecam** com comentario. Nao tentei remover comentario no fim da linha nem
dentro de string: `https://` viraria vitima, e o codigo da linha continua sendo evidencia
legitima de qualquer forma.

### O sinal que denunciou
O resultado ficou **bom demais**. Cinco regras "ok" logo depois de mexer em duas.
Quando uma auditoria melhora mais do que o trabalho feito justifica, o problema esta na
auditoria — vale conferir uma por uma antes de comemorar.

## Relacionado
- [[auditoria-por-evidencia-em-vez-de-sast]]
- [[Cerebro]]
