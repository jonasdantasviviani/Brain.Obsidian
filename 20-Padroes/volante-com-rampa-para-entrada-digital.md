---
tipo: padrao
titulo: Volante com rampa - tecla e dedo so dizem liga-desliga
projeto: [RagdollGames]
stack: [flutter, dart]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [volante, rampa, suavizar, teclado, toque, entrada digital, comando, sensibilidade, jogo de corrida, ComandoSuave]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Volante com rampa para entrada digital
## Resumo
Entre o `Comando` (liga-desliga) e a fisica entra uma rampa que leva o volante ao fim do curso
aos poucos e o devolve ao centro **mais depressa** do que o tirou.

## Contexto
`Need for Ragdoll`, 2026-09-20. Seta do teclado e lado do dedo so sabem -1, 0 ou 1; mandar
isso cru para a fisica poe o volante no fim do curso em um quadro, e o carro anda aos
solavancos - o defeito classico de jogo de corrida no teclado.

## Detalhe
```dart
final voltando =
    atual != 0 && (alvo == 0 || alvo.sign != atual.sign);   // sair do centro NAO e voltar
final passo = rampaPorSegundo * (voltando ? 1.8 : 1) * dt;
atual = (alvo - atual).abs() <= passo ? alvo : atual + passo * (alvo - atual).sign;
```
- **Voltar mais rapido que sair** (1,8x): senao o carro fica "preso" na curva depois que a
  mao ja largou a tecla.
- **Inverter o lado passa pelo centro**, nao pula de 1 para -1.
- A rampa vira ajuste do jogador: leve (2,6/s), normal (5/s), direto (sem rampa).
- Acelerador e freio de mao passam inteiros - so o volante precisa de rampa.
- Mora no **dominio** (`domain/carro/comando_suave.dart`) e roda dentro do passo fixo.

Erro que ja aconteceu: comparar `alvo.sign != atual.sign` sem checar `atual != 0` faz a
**saida do centro** usar a velocidade de volta.

## Relacionado
- [[fisica-de-carro-arcade-visto-de-cima]]
- [[motor-idle-em-flutter]]
