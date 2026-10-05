---
tipo: seguranca
titulo: 28. Backup testado e protegido
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [backup, restore, restauracao, ransomware, bak, retencao, 3-2-1, desastre, recuperacao, teste de restauracao]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 28. Backup testado e protegido
## Resumo
Backup que nunca foi restaurado nao e backup, e backup alcancavel pela aplicacao e alvo de ransomware.

## Contexto
Regra 28. A [[BTech.NFe.Api]] ja lida com `.bak` e tem
`POST /api/admin/database/validate-local-backup` — infraestrutura na direcao certa, que esta
regra completa.

## Detalhe

### As tres perguntas que definem se ha backup
1. **Quando foi a ultima restauracao de teste?** Sem resposta, nao ha backup — ha arquivos.
2. **Quanto se perde no pior caso?** (RPO) A frequencia do backup e o tamanho da perda aceita.
3. **Quanto tempo leva para voltar?** (RTO) Restaurar 200 GB nao e instantaneo; medir e a unica
   forma de saber.

### Regra 3-2-1
Tres copias, em dois tipos de midia, uma **fora do ambiente**. A copia de fora e a que salva de
ransomware, exclusao acidental e comprometimento da conta de nuvem.

### O ponto mais esquecido: o backup e alvo
Ransomware moderno **procura e apaga backup antes** de criptografar. Se a mesma credencial da
aplicacao alcanca o backup, ele nao protege de nada.

- Credencial **separada**, sem permissao de exclusao
- Armazenamento **imutavel** / WORM, com retencao travada (Azure Blob immutability, S3 Object
  Lock)
- Alerta quando um backup e apagado

### Backup e dado em repouso
O `.bak` tem **todos** os CPFs, CNPJs e valores do banco, sem nenhuma das protecoes da aplicacao.
- Criptografia em repouso, com a chave guardada **em outro lugar** (backup criptografado com a
  chave ao lado nao ajuda)
- Mesmas regras de acesso do banco de producao
- Cuidado ao restaurar producao em desenvolvimento: isso espalha dado pessoal para um ambiente
  com menos controle. **Mascare ao restaurar fora de producao.**

### Testar de verdade
Restauracao automatica periodica em ambiente isolado, com verificacao de integridade
(contagem de linhas, checksum, uma consulta de negocio conhecida). A rota
`validate-local-backup` que ja existe e a semente disso — falta a periodicidade e o alerta.

## Relacionado
- [[05-criptografar-dados-sensiveis]]
- [[BTech.NFe.Api]]
- [[docker-compose-nos-projetos]]
