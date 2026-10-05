---
tipo: sistema
titulo: Protocolo do Cerebro
tags: [cerebro/sistema]
atualizado: 2026-09-07
---

# Protocolo do Cerebro

Regras que **todo agente** (Claude Code, Codex, qualquer outro) segue neste computador.
Nada aqui depende do usuario pedir. E automatico.

## 1. Antes de qualquer tarefa nova — RECUPERAR

O hook `UserPromptSubmit` ja injeta automaticamente as notas relacionadas ao pedido.
Quando precisar de mais contexto, ou quando o agente nao tiver hooks (Codex):

```bash
python3 ~/.claude/cerebro/cerebro.py recall "<assunto da tarefa>"
```

Leia com `Read` as notas realmente relevantes **antes** de escrever codigo.
Se uma nota contradiz o que voce ia fazer, siga a nota — ela e memoria do usuario.

## 1b. Sempre — as 28 REGRAS DE SEGURANCA

O hook `SessionStart` audita o projeto atual contra as 28 regras obrigatorias e injeta as
pendencias. Elas estao em `80-Seguranca/`, uma nota por regra, e o estado consolidado em
[[Checklist-Seguranca]].

- Se a tarefa da sessao **tocar** uma area com pendencia, corrija de passagem.
- Nunca introduza codigo novo que viole uma das 20.
- Auditar um projeto a mao: `cerebro seguranca <caminho>`.

## 2. Depois de qualquer entrega — REGISTRAR

Obrigatorio, sem perguntar. O hook `Stop` cobra isso automaticamente.

| Aconteceu | Nota |
| --- | --- |
| Qualquer sessao com trabalho entregue | `70-Sessoes/AAAA-MM-DD-<projeto>-<slug>.md` |
| Descobriu/mudou estrutura, stack, como rodar | `10-Projetos/<Projeto>.md` |
| Solucao reutilizavel em outros projetos | `20-Padroes/<Padrao>.md` |
| Escolha tecnica com alternativa descartada | `30-Decisoes/<Decisao>.md` |
| Usuario corrigiu voce ou expressou uma regra | `40-Preferencias/<Preferencia>.md` |
| Aprendeu sobre lib, framework, comando, versao | `50-Stack/<Tecnologia>.md` |
| Erro que custou tempo | `60-Armadilhas/<Erro>.md` |

Regras de qualidade:
- **Atualizar** nota existente sempre que possivel. Duplicata e ruido.
- Registrar so o que voce gostaria de reler daqui a 6 meses.
- Comandos que funcionaram valem mais que prosa.
- Ligar notas entre si com wikilinks `[[nome-da-nota]]` — e isso que faz o grafo servir para algo.

## 3. Frontmatter obrigatorio

```yaml
---
tipo: projeto | padrao | decisao | preferencia | stack | armadilha | sessao
titulo: Titulo legivel
projeto: [NomeDoProjeto]
stack: [dotnet, react]
tags: [tipo/padrao, stack/dotnet]
palavras-chave: [termos, que, voce, usaria, para, reencontrar]
origem: claude-code | codex
criado: AAAA-MM-DD
atualizado: AAAA-MM-DD
confianca: alta | media | baixa
---
```

Secoes do corpo: `## Resumo` (uma linha), `## Contexto`, `## Detalhe`, `## Relacionado`.
O campo `palavras-chave` e o que mais pesa na busca — capriche nele.

## 4. Reindexar

```bash
python3 ~/.claude/cerebro/cerebro.py index
```

Roda sozinho ao fim de cada sessao e a cada escrita no vault.
