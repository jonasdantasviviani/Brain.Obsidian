---
tipo: decisao
titulo: Captura via Stop hook em vez de digest em segundo plano
projeto: [Cerebro]
stack: [claude-code, python]
tags: [tipo/decisao, cerebro/sistema]
palavras-chave: [stop hook, captura, memoria, digest, headless, automacao, transcript]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Captura via Stop hook em vez de digest em segundo plano

## Resumo
O registro no vault e feito pelo proprio modelo no fim da sessao, nao por um processo separado.

## Contexto
Precisava de registro 100% automatico. Duas opcoes reais.

## Detalhe

**Escolhido — `Stop` hook com `decision: "block"`.**
O hook analisa o transcript, decide se houve entrega real e devolve o protocolo de registro
como `reason`. O modelo continua o turno e escreve as notas com todo o contexto da sessao na cabeca.

- Qualidade alta: quem resume e quem viveu a sessao.
- Custo: um turno extra por sessao com trabalho.
- Risco de loop resolvido com duas guardas: `stop_hook_active` e o contador `tentativas`
  no arquivo de estado da sessao.

**Descartado — digest headless (`claude -p`) em background.**
Um processo separado leria o transcript e escreveria as notas sem interromper nada.

- Nao interromperia o fluxo, mas o CLI `claude` **nao esta no PATH** desta maquina
  (so existe o app `/Applications/Claude.app`), entao a peca central nao existiria.
- Custaria uma chamada de modelo inteira so para reler o que ja estava em contexto.

**Gatilho de "houve entrega"** (`config.json` → `gatilho_captura`):
1 edicao fora do vault, ou 4 comandos bash, ou 6 turnos do assistente.
Sessoes de pergunta e resposta simples nao geram nota — isso evita afogar o vault em ruido.

## Relacionado
- [[Como-Funciona]]
- [[2026-09-07-cerebro-montagem-do-sistema]]
