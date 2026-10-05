---
tipo: padrao
titulo: Fisica de carro arcade visto de cima - volante limitado pelo pneu e circulo de atrito
projeto: [RagdollGames]
stack: [flutter, dart, flame, forge2d]
tags: [tipo/padrao, stack/flutter, dominio/jogos, jogo/ragdoll]
palavras-chave: [carro, drift, derrapagem, atrito lateral, top-down, arcade, volante, freio de mao, circulo de atrito, oversteer, box2d, slip angle]
origem: claude-code
criado: 2026-09-20
atualizado: 2026-09-20
confianca: alta
---

# Fisica de carro arcade visto de cima
## Resumo
Duas pecas - o volante gira o carro dentro do que o pneu sustenta, o pneu puxa a velocidade de
volta para a direcao do capo - e o drift nasce do desencontro entre as duas.

## Contexto
`Need for Ragdoll`, dia 6 do sprint. Primeira tentativa foi o modelo classico de dois eixos
(iforce2d): cancelar a velocidade lateral **no ponto de cada eixo**, aplicando o impulso ali
para ganhar o torque de alavanca. **Nao funciona quando o volante tambem impoe a velocidade
angular**: em curva, a velocidade lateral de cada eixo vale `ω * distancia_ao_centro` mesmo com
o carro perfeitamente agarrado, entao o pneu passa a lutar contra a propria curva. O resultado
foi carro que quase nao virava (giro 0,14 rad/s com o volante no fundo) ou que rodava de vez.

Ou se modela o esterco de verdade (pneu dianteiro com eixo lateral girado pelo angulo de
esterco, sem impor ω), ou se assume o modelo arcade. Para jogo de celular, arcade.

## Detalhe

### As duas pecas, nesta ordem
```dart
// 1. o volante gira o carro - mas nunca mais do que o pneu aguenta
final rendimento = (velocidade / velocidadeDeGiroPleno).clamp(0, 1); // parado nao vira
final aderencia = (dianteira + traseiraAgora) / 2 * 0.85;            // 15% de margem
final peloPneu = aderencia * oversteer / velocidade;                 // a = v * ω
final alvo = volante * min(giroMaximo, peloPneu) * rendimento;
deltaAngular = (alvo - ω) * (1 - exp(-resposta * dt));

// 2. o pneu puxa a velocidade de volta para a direcao do capo
final teto = aderencia * dt;
deltaLateral = (-velocidadeLateral).clamp(-teto, teto);
```

**A velocidade da conta e a velocidade inteira, nao a componente frontal.** Usar so a frente
faz o carro girar cada vez mais depressa conforme atravessa - a espiral que termina com ele
rodando toda vez.

### O circulo de atrito e quem cria o drift
O pneu tem **um orcamento so**, dividido entre empurrar e segurar:

```dart
aderenciaTraseiraAgora = sqrt(max(0, base² - tracao²))   // piso de 1 m/s2
```

Acelerar fundo na curva gasta o orcamento da traseira empurrando, e o que sobra nao segura o
carro. **O drift acontece sem o jogador pedir** - que e exatamente o que um roteiro quer da
primeira curva do jogo. O freio de mao e o mesmo efeito pedido de proposito (`base` cai para
2,5 m/s2).

### Oversteer: traseira solta gira mais
```dart
oversteer = min(aderenciaTraseiraNominal / aderenciaTraseiraAgora, 1.4)
```
Com a traseira perdendo aderencia, o carro **gira mais** e **segura menos** ao mesmo tempo: o
nariz entra e a traseira sai. Sem o teto de 1,4 vira piao.

### Derrapagem se mede pelo angulo de deriva, nao pelo pneu
```dart
derrapagem = (|velLateral| / velocidade) / sin(35°)  // vezes uma porta de velocidade minima
```
Medir pela **saturacao do pneu** parece certo e da errado: em qualquer curva o pneu satura um
tanto, e o medidor acusa drift so de virar o volante. O que o jogador chama de drift e o carro
apontando para um lado e indo para outro.

### Velocidade maxima derivada, nao clampada
```dart
arrasto = (aceleracao - resistenciaAoRolamento) / velocidadeMaxima²
```
O teto e onde motor e arrasto empatam. Assim ele nao e um `clamp` escondido: o carro chega
perto e para de ganhar, como carro de verdade.

### Onde isso mora
A conta inteira fica em `domain/`, em **velocidade** (m/s e rad/s), nunca em newtons. Quem
multiplica pela massa e pela inercia e `infrastructure/fisica/corpo_carro.dart`, que e quem
conhece o corpo. Ganho pratico: da para rodar mil passos num teste de milissegundos com um
integrador de papel, e ver se o carro anda de lado - em vez de olhar a tela e achar.

**O integrador de teste precisa girar o referencial**, senao a velocidade lateral nunca
aparece numa curva e todo teste de drift passa por engano:
```dart
frente += giro * lateralAntes * dt;
lateral -= giro * frenteAntes * dt;   // a Box2D faz isso de graca, em coordenadas de mundo
```

## Relacionado
- [[jogo-flame-estende-base-de-fisica-na-infra]]
- [[ragdoll-em-flutter-com-forge2d]]
- [[junta-que-quebra-lendo-constraint-force]]
- [[need-for-ragdoll-visto-de-cima-sem-gravidade]]
