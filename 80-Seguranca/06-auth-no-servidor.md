---
tipo: seguranca
titulo: 06. Auth server side
projeto: [todos]
stack: []
tags: [tipo/seguranca, cerebro/regra-obrigatoria, seguranca]
palavras-chave: [autenticacao, autorizacao, servidor, jwt, middleware, authorize, guard, cliente, bypass, endpoint, rota, protegido, permissao, acesso, perfil, papel]
origem: claude-code
criado: 2026-09-07
atualizado: 2026-09-07
confianca: alta
---

# 06. Auth server side
## Resumo
Toda decisao de autenticacao e autorizacao acontece no servidor. Guard de rota no cliente e usabilidade, nao seguranca.

## Contexto
Regra 6 das 20 obrigatorias.

## Detalhe

### A linha que separa
| No cliente | No servidor |
| --- | --- |
| esconder botao, redirecionar rota | **decidir se a operacao acontece** |
| melhorar UX | validar token, checar papel, checar dono do registro |

Quem chama sua API com `curl` nao executa o seu React. Se a unica barreira e um
`if (!user) redirect('/login')` no front, **nao ha barreira**.

### Como fazer
- .NET: `AddAuthentication` + `[Authorize]` nos controllers, politicas para papeis
  (a [[BTech.NFe.Api]] usa a politica `SuperUsuario` exigindo claim `super_usuario = "S"`)
- Next: verificacao no `middleware.ts` **e** em cada route handler / server action.
  Middleware sozinho ja foi contornado por bug de matcher — nao dependa so dele.

### Nao esqueca
Autenticado ≠ autorizado. Depois de saber **quem e**, ainda e preciso checar
**se pode neste registro** → [[07-travar-acesso-aos-registros]].

## Relacionado
- [[07-travar-acesso-aos-registros]]
- [[09-proteger-cookies-de-sessao]]
