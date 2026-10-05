---
tipo: armadilha
titulo: Suprimir aviso de vulnerabilidade com NoWarn em vez de resolver
projeto: [BTech.NFe.Api]
stack: [dotnet, nuget]
tags: [tipo/armadilha, stack/dotnet, seguranca, cerebro/critico]
palavras-chave: [nowarn, nu1903, vulnerabilidade, supressao, nuget, audit, automapper, dependencia, cve, silenciar aviso]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# Suprimir aviso de vulnerabilidade com NoWarn em vez de resolver
## Resumo
Um `<NoWarn>NU1903</NoWarn>` escondeu por meses uma vulnerabilidade High num pacote que nem estava sendo usado.

## Contexto
Encontrado em 07/09/2026 na [[BTech.NFe.Api]], durante a primeira auditoria das 20 regras.

## Detalhe

### O que estava no csproj
```xml
<!-- NuGetAuditSuppress: mapeamentos são todos hardcoded, sem entrada de usuário -->
<PackageReference Include="AutoMapper" Version="14.0.0">
  <NoWarn>NU1903</NoWarn>
</PackageReference>
```

O raciocinio do comentario **estava tecnicamente correto**: a CVE-2026-32933 e um DoS por
recursao em objeto aninhado (~25 mil niveis), e os mapeamentos eram fixos. O erro nao foi a
analise — foi a **conclusao**: silenciar em vez de resolver.

### O que a investigacao revelou
- 27 `CreateMap<X, X>()` — todos **identidade**, nenhum mapeamento real
- **Zero** injecoes de `IMapper` em todo o codigo e nos 4 projetos de teste
- O pacote era **dependencia morta**

A correcao certa nao era atualizar nem suprimir: era **remover**.
Resultado: 0 avisos no build, 392 testes passando, e
`dotnet list package --vulnerable` limpo em toda a solucao.

### Por que a supressao e pior do que parece
1. `NoWarn` cala o aviso para **sempre**, inclusive para CVEs futuras do mesmo pacote
2. O comentario justifica o risco de **hoje**; ninguem revisita quando o uso muda
3. Some do radar: o CI passa verde e a auditoria automatica so pega porque le o csproj

### A regra
Vulnerabilidade tem tres saidas legitimas — **atualizar**, **remover** ou **trocar**.
Suprimir nao e uma delas. Se o risco for mesmo aceitavel, registre uma decisao com data de
revisao, nao um `NoWarn` eterno.

### Detalhe que engana
`dotnet list package --vulnerable` **sai com codigo 0** mesmo achando vulnerabilidade.
Um passo de CI que so confia no exit code passa verde. Por isso o scan precisa de grep:

```bash
dotnet list package --vulnerable --include-transitive > v.txt
grep -q "has the following vulnerable packages" v.txt && exit 1
```

## Relacionado
- [[20-scan-de-dependencias]]
- [[BTech.NFe.Api]]
- [[2026-09-07-correcao-das-pendencias-de-seguranca]]
