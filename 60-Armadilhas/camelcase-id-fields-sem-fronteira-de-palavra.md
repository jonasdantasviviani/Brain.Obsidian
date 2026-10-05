---
tipo: armadilha
titulo: Campo Id<palavra-minuscula> do legado vira JSON tudo minusculo, nao camelCase
projeto: [BTech.NFe.Api, BTech.Web]
stack: [dotnet, aspnetcore, csharp, next, typescript]
tags: [tipo/armadilha, stack/dotnet, stack/next, empresa/btech]
palavras-chave: [JsonNamingPolicy, CamelCase, System.Text.Json, JsonPropertyName, casing, idempresa, idcliente, idpagto, idvendedor, CabPedido, CabNota, Fornecedore, serializacao]
origem: claude-code
criado: 2026-09-16
atualizado: 2026-09-16
confianca: alta
---

# Campo Id<palavra-minuscula> do legado vira JSON tudo minusculo, nao camelCase
## Resumo
Toda entidade scaffoldada do legado com um campo tipo `Idpagto`, `Idempresa`, `Idcliente`,
`Idvendedor` (so o "I" de "Id" maiusculo, resto colado em minusculo) serializa via
`JsonNamingPolicy.CamelCase` como `idpagto`/`idempresa`/etc — **nao** `idPagto`/`idEmpresa` como o
resto do app espera. Confirmado empiricamente (nao e suposicao): rodei um console .NET chamando
`JsonNamingPolicy.CamelCase.ConvertName("Idpagto")` e deu `"idpagto"`.

## Contexto
Achado em 2026-09-16 investigando por que o filtro de Cliente/Vendedor/Condicao de Pagamento na
listagem de Pedidos do [[BTech.Web]] sempre voltava vazio, e por que salvar um pedido editado sem
reselecionar Empresa/Cliente/Vendedor apagava esses campos. `CabPedido.Idempresa/Idcliente/Idpagto/
Idvendedor` sao assim porque foram scaffoldados direto do nome da coluna SQL legada (`Idpagto`, uma
palavra so). Campos que TEM uma letra maiuscula interna (ex.: `IdcondPagto`, `IdformaPagto`,
`IdtipoPagto`, `CodCliente`, `RazaoSocial`) convertem certo porque o CamelCase policy do .NET usa
essa maiuscula interna como fronteira de palavra — so quebra quando a fronteira nao existe no nome
C#.

## Detalhe

### Regra pratica
Antes de consumir um campo `Id<coisa>` vindo de uma entidade scaffoldada (ou de expor um novo campo
assim numa tela), rode o teste rapido:
```csharp
Console.WriteLine(System.Text.Json.JsonNamingPolicy.CamelCase.ConvertName("Idpagto"));
// "idpagto" -> quebra. "IdcondPagto" -> "idcondPagto", ok.
```
Ou simplesmente: se so a primeira letra do nome C# e maiuscula (uma palavra so colada), o JSON sai
tudo minusculo. Se tem uma segunda maiuscula no meio, essa vira a fronteira do camelCase e funciona.

### Onde ja apareceu / correcao aplicada
- `CabPedido.Idempresa/Idcliente/Idpagto/Idvendedor` — **corrigido** com `[JsonPropertyName]`
  explicito (`"idEmpresa"`, `"idCliente"`, `"idPagto"`, `"idVendedor"`) direto na entidade
  (`src/Domain/BTech.NFe.Domain/Entities/CabPedido.cs`), porque o frontend de Pedidos (novo, editar,
  lista com filtro) ja usava `idEmpresa`/`idCliente`/`idPagto`/`idVendedor` em todo lugar — era mais
  seguro alinhar o backend ao que o frontend ja pressupunha (e o unico ponto de mudanca) do que caçar
  cada leitura espalhada pelo frontend.
- `Fornecedore.Idvendedor` — mesma correcao, preventiva, porque a tela nova de Fornecedores
  (`/dashboard/fornecedores`) usa um select de Vendedor.
- `CabNota.Idempresa/Idcliente` — **tem o mesmo problema e NAO foi corrigido**: o frontend de Notas
  Fiscais (`notas-fiscais/nova/page.tsx`) por acidente ja usa `idempresa`/`idcliente` minusculo (bateu
  com o bug sem querer), entao mexer no backend ali quebraria o que hoje funciona. Se um dia alguem
  "corrigir" esse frontend para `idEmpresa`/`idCliente` (parece mais certo, mas nao e), vai quebrar —
  documentado aqui pra quem passar por isso nao se confundir.

### Outros campos suspeitos (nao verificados, so listados pelo padrao do nome)
Achei via `grep -rhoE "public int\??\s+Id[a-z][A-Za-z]*"` nas entidades: `Idcentrocusto`,
`Idcliente`, `Idcontrato`, `Idcredito`, `Iddebito`, `Identrada`, `Idforne`, `Idnota`, `Idos`,
`Idparametro`, `Idpedido`, `Idperfil`, `Idproduto`, `Idprestador`, `Idregra`, `Idrepre`,
`Idrepresentante`, `Idtransporte`, `Idusuario` (e variantes) em varias entidades. **`Idperfil`
aparece 18x no frontend** — mas o de login/`LoginResponse` provavelmente esta OK porque e um DTO
escrito a mao (nome de propriedade decidido pelo dev, nao scaffoldado), so quem usa a entidade
`Usuario` crua direto (`UsuarioController`?) e que pode ter o mesmo problema. `Idproduto` aparece
12x (provavelmente em itens de pedido/nota — `CorPedido`/`CorNota`). **Nao auditado a fundo** — vale
uma varredura futura se aparecer sintoma parecido (filtro que nunca bate, campo que some ao editar).

## Relacionado
- [[BTech.NFe.Api]]
- [[BTech.Web]]
- [[2026-09-16-btechplus-frontend-comparacao-telas]]
- [[nullable-enable-transforma-coluna-legada-em-campo-obrigatorio]]
