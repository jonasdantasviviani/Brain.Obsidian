---
tipo: stack
titulo: Cobrança Sicoob (756) — layouts CNAB 400/240, DV do nosso número e fator de vencimento
projeto: BTech
stack: [dotnet, cnab, sicoob]
tags: [boleto, cnab, financeiro]
palavras-chave: [Sicoob, Bancoob, CNAB 240, CNAB 400, nosso número, linha digitável, fator de vencimento, remessa, retorno]
origem: pedido do Jonas 2026-10-07
criado: 2026-10-07
atualizado: 2026-10-07
confianca: media
---
- Fonte oficial usada: planilha "Layouts para troca de informações" do Sicoob (fev/2017), no repo eduardokum/laravel-boleto (`manuais/BANCOOB-SICOOB`). O "Manual Layout Sicoob.pdf" é OUTRO layout (legado SX, nosso número 11 dígitos): não usar.
- Nosso número = 7 dígitos + DV (pesos 3-1-9-7 sobre agência(4)+cliente(10)+nº(7), mód. 11, resto 0/1 = 0). Código do cliente com DV, até 7 dígitos.
- Fator de vencimento: base 07/10/1997=0; em 22/02/2025 voltou a 1000 (nova base). 
- ParametrosBoletos (legado) tem chave banco+ativo: um convênio Sicoob por tenant.
- Pendente: homologar arquivo-teste com o Sicoob. Implementação em API #103, Web #79. Ver [[BTech.NFe.Api]].
