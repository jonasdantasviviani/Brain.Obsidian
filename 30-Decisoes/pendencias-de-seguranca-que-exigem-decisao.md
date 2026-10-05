---
tipo: decisao
titulo: Pendencias de seguranca que exigem decisao do Jonas
projeto: [BTech.NFe.Api, Heavy, ICook, BTech.Web]
stack: [dotnet, next, seguranca]
tags: [tipo/decisao, seguranca, cerebro/regra-obrigatoria]
palavras-chave: [pendencia, decisao, seguranca, mass assignment, criptografia, cpf, turnstile, captcha, localstorage, refatoracao, backlog, risco]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Pendencias de seguranca que exigem decisao do Jonas
## Resumo
O que sobrou da correcao automatica de 07/09/2026, com o motivo de cada adiamento e o caminho para resolver.

## Contexto
Na sessao [[2026-09-07-correcao-das-pendencias-de-seguranca]] as pendencias mecanicas foram
resolvidas. As de baixo dependem de decisao de produto, credencial externa ou refatoracao
grande — fazer sem validar seria pior que nao fazer.

## Detalhe

### 1. Mass assignment + trim de respostas na [[BTech.NFe.Api]] (regras 08 e 17)
**Situacao:** ~27 controllers recebem e devolvem entidades EF scaffolded do banco legado
(`[FromBody] CabNota`). Cada endpoint aceita e expoe **todas as colunas da tabela**.

**Por que nao fiz:** e a maior superficie de risco do checklist, mas exige criar um
`*Request` e um `*Response` por endpoint de escrita e leitura. Sao dezenas de arquivos e
mudanca de contrato publico — o [[btech-nfe-web]] consome esses formatos hoje.

**Caminho:** fatiar por dominio, comecando pelos que expoem dado sensivel
(Cliente, Empresa, Formula/usuario). Cada fatia: criar DTO → ajustar controller →
rodar os testes de contrato → ajustar o front. O projeto ja tem
`BTech.NFe.Tests.Contract`, que trava o formato — use-o como rede.

### 2. Criptografia de CPF/CNPJ (regra 05) — [[Heavy]] e [[BTech.NFe.Api]]
**Situacao:** `cpf`, `cnpj` e `cartao` em claro nas migrations do Heavy; CNPJ na BTech.

**Por que nao fiz:** muda schema, muda queries e pode quebrar busca por documento.
E no caso da BTech e **discutivel**: CNPJ de emitente e dado publico da nota fiscal.

**Caminho:** decidir por campo, nao em bloco. Para o CPF do motorista (Heavy), o passo de
maior retorno e mais barato e **mascarar na resposta da API** (`***.456.789-**`) —
resolve o vazamento sem tocar no banco. Cripto em repouso so se o modelo de ameaca pedir.

### 3. Hash de senha no [[Heavy]] (regra 10)
**Situacao:** a coluna `senha_hash` existe nas migrations; nenhum algoritmo implementado.

**Por que nao fiz:** o login com senha ainda nao foi construido. Escrever a implementacao
agora seria adivinhar o fluxo de autenticacao.

**Caminho:** quando construir, **Argon2id** desde a primeira linha, e nunca `==` na
comparacao. Ver [[10-hash-nas-senhas]].

### 4. Bot protection nas APIs e apps (regra 12)
**Situacao:** resolvido nos [[Sites-Estaticos]] com [[honeypot-anti-bot-em-formulario]].
Falta no login das APIs e nos apps Next.

**Por que nao fiz:** Turnstile/reCAPTCHA exigem **chave do provedor** — nao da para
configurar sem a conta do Jonas.

**Caminho:** criar o site no Cloudflare Turnstile (gratis), guardar a chave como secret e
validar o token **no servidor**. O rate limit ja existente na BTech.NFe.Api cobre parte do
risco enquanto isso.

### 5. Token de sessao em `localStorage` no [[BTech.Web]] (regras 06 e 09)
**Situacao:** o token fica em `localStorage`, alcancavel por qualquer script da pagina.

**Por que nao fiz:** trocar para cookie `HttpOnly` exige mudanca **nos dois lados** —
a API precisa emitir e ler o cookie, e o front para de gerenciar o token. E refatoracao
de autenticacao inteira, com risco de derrubar o login em producao.

**Caminho:** a API passa a emitir cookie `HttpOnly; Secure; SameSite=Lax`; o front remove o
gerenciamento manual. Fazer atras de uma flag e validar com os testes Playwright existentes
(o `playwright/.auth` ja guarda estado de sessao). Ver [[09-proteger-cookies-de-sessao]].

### 6. RLS, auth e DTOs no [[ICook]] (regras 04, 06, 07, 17)
**Situacao:** o backend C# tem **1 arquivo** (`Program.cs`). Nao ha schema, auth nem endpoint.

**Por que nao fiz:** as pendencias sao de codigo que ainda nao existe. Alem disso o
`CLAUDE.md` do projeto pede confirmacao antes de qualquer plano de Phase 1, e a Phase 0
ainda nao foi concluida.

**Caminho:** essas quatro regras entram como criterio de aceite das primeiras tarefas do
backend — nao como divida a pagar depois. RLS no primeiro `CREATE TABLE`, `[Authorize]` no
primeiro endpoint, DTO no primeiro retorno.

## Relacionado
- [[Checklist-Seguranca]]
- [[2026-09-07-correcao-das-pendencias-de-seguranca]]
- [[08-bloquear-mass-assignment]]
- [[17-trim-nas-respostas-de-api]]
