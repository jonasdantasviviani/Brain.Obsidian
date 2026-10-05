---
tipo: padrao
titulo: Dominio em portugues, tecnico em ingles
projeto: [Heavy]
stack: [dotnet, csharp, postgresql]
tags: [tipo/padrao, stack/dotnet, cerebro/padrao-obrigatorio]
palavras-chave: [idioma, nomenclatura, convencao, portugues, ingles, glossario, snake_case, naming]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Dominio em portugues, tecnico em ingles
## Resumo
Entidades, tabelas, rotas e eventos em portugues; termos de arquitetura e da linguagem em ingles.

## Contexto
Convencao do [[Heavy]] (doc 26, §2). Resolve a briga de "traduzir ou nao" de uma vez.

## Detalhe

| Portugues | Ingles |
| --- | --- |
| entidades, servicos de dominio, enums: `Ordem`, `Jornada`, `Motorista`, `MotivoFalha` | `Repository`, `Service`, `Extensions`, `Options`, `Controller`, `Result<T>` |
| tabelas e colunas: `ordem_servico`, `concluida_em` | palavras-chave e tipos da linguagem |
| rotas: `POST /v1/ordens/{id}/concluir` | — |
| eventos: `ordem.entregue` | — |

- Comentarios, mensagens de commit e documentacao: **portugues**.
- **Sem acento nem cedilha em identificador**: `ordem_servico`, nao `ordem_serviço`.
- JSON e banco em `snake_case`, configurado uma vez e esquecido
  (`JsonNamingPolicy.SnakeCaseLower` + `UseSnakeCaseNamingConvention()`).
  **Nunca atributo em propriedade.**

### Verbos com significado fixo
| Verbo | Significa |
| --- | --- |
| `Criar` | cria |
| `Atualizar` | atualiza |
| `Aplicar` | transicao de estado |
| `Registrar` | fato imutavel |
| `Calcular` | calcula |
| `Validar` | **retorna** resultado, nao lanca |
| `Garantir` | **lanca** se nao valer |

## Relacionado
- [[Heavy]]
- [[idioma-e-comunicacao]]
