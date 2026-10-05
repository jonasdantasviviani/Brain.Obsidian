---
tipo: sessao
titulo: Aplicar as skills de arquitetura nos repos da BTech
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, next]
tags: [tipo/sessao, stack/dotnet, empresa/btech]
palavras-chave: [skill, backend-architecture, btech, refatoracao, camadas, dotnet 10, csproj, dependencias, arquitetura]
origem: claude-code
criado: 2026-08-11
atualizado: 2026-09-07
confianca: alta
---

# Aplicar as skills de arquitetura nos repos da BTech
## Resumo
Quatro sessoes ao longo de agosto reestruturando os repos da BTech para obedecer a skill `backend-architecture`.

## Contexto
11, 13 e 19/08/2026, em `Repos/BTech`. As skills ficam em `Repos/BTech/.claude/skills/`.
Total aproximado: 366 comandos bash, 119 arquivos tocados.

## Detalhe

### Os pedidos, na ordem
1. "rode todas as skills e ajuste os projetos"
2. "Continue com os ajustes levantados pelas skills"
3. "veja todos os repos se estao de acordo das skills, **o back tem q ser .net 10 por exemplo.
   Siga a risca as skills**"
4. "Continue ajustando os projetos de acordo com as skills, siga a risca todas"
5. "instalei o .net 10. veja novamente. **Troque todos os csproj para usar o 10** e tambem veja
   se precisa atualizar as dependencias dos projetos"

### O que resultou (commits da BTech.NFe.Api)
```
651e664 composition root + service layer para controllers
24657fe remove dependencia Application -> Infrastructure.Sql
283fe63 move interfaces de servico e DTOs para o Domain
d9d0f7a uma pasta por camada sob src/
6f1c444 migra para .NET 10 e forca o code style de backend-architecture
```

### Padrao de trabalho do Jonas que aparece aqui
Ele nao pede "refatore" solto — ele pede **"siga a risca a skill"**. A skill e a especificacao;
o trabalho e conformar o codigo a ela. Ver [[skills-por-pasta-no-claude]].

## Relacionado
- [[BTech.NFe.Api]]
- [[arquitetura-dotnet-em-camadas]]
- [[skills-por-pasta-no-claude]]
- [[dotnet-10-como-padrao-de-backend]]
