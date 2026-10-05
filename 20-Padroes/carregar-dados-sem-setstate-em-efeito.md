---
tipo: padrao
titulo: Carregar dados sem setState dentro de efeito (react-hooks/set-state-in-effect)
projeto: [BTech.Web]
stack: [react, next, typescript]
tags: [tipo/padrao, stack/react, stack/next]
palavras-chave: [set-state-in-effect, react compiler, eslint-plugin-react-hooks 7, useEffect, setState, useCarregar, useSyncExternalStore, useEffectEvent, localStorage, matchMedia, hidratacao, mounted, estado derivado, ajustar estado no render, cascading renders, loading]
origem: claude-code
criado: 2026-09-11
atualizado: 2026-09-11
confianca: alta
---

# Carregar dados sem setState dentro de efeito (react-hooks/set-state-in-effect)
## Resumo
A regra do React Compiler proibe setState sincrono no corpo do efeito; cada caso tem um substituto recomendado pelo React — busca com setState so nos callbacks da promise, useSyncExternalStore para fonte externa, estado derivado ou ajuste durante o render.

## Contexto
[[BTech.Web]], 2026-09-11: 47 ocorrencias em 41 arquivos migradas; a regra voltou a "error".

## Detalhe

### O que a regra rastreia (lido no codigo do plugin, `validateNoSetStateInEffects`)
- Marca `setState` chamado direto no corpo do efeito **e** chamada a funcao local que chame
  setState em qualquer ponto — **inclusive depois de `await`**. Por isso
  `useEffect(() => { carregar() }, [])` e marcado mesmo com `carregar` assincrono.
- Aceita setState dentro de callback passado a outra funcao (`.then(...)`, `setInterval(...)`).
- Funcao recebida por parametro (ex.: dentro de um hook) nao e rastreada.

### Substituto por caso
| Caso | Padrao |
| --- | --- |
| Buscar dados (lista, relatorio, paginacao) | `useCarregar(fn, deps, { aoFalhar, ativo })` → `{ dados, carregando, erro, recarregar }` |
| Estado editavel alimentado por busca (form, toggle otimista) | efeito com `.then(r => { if (atual) setX(r) })` + `loading` derivado de "carregado para qual id" |
| "Estou no cliente?" (hidratacao) | `useNoCliente()` = `useSyncExternalStore(() => () => {}, () => true, () => false)` |
| localStorage | store com `gravarValorLocal` que avisa ouvintes + `useValorLocal(chave)` (uSES) |
| matchMedia | `useSyncExternalStore(assinar, () => window.innerWidth < BP, () => false)` |
| Zerar estado quando prop muda (dialogo abre/fecha) | ajuste no render: `const [ant, setAnt] = useState(prop); if (prop !== ant) { setAnt(prop); ...reset }` |
| Fechar flyout ao trocar de rota | guardar a rota junto (`{ group, path }`) e derivar |

### useCarregar (src/hooks/useCarregar.ts)
- `carregando` **derivado**: true enquanto o ultimo resultado nao e das deps/versao atuais —
  nao precisa `setLoading(true)` antes da busca.
- `recarregar()` = `setVersao(v => v + 1)` (chamado de evento ou de `setInterval`).
- Erro mantem os dados anteriores (como as telas faziam) e chama `aoFalhar` (toast).
- Resposta de busca antiga e descartada (`let atual`), o que corrige corrida de paginacao rapida.
- `useEffectEvent` (estavel no React 19.2) para `carregar`/`aoFalhar` sem entrarem nas deps.
- Polling com "Carregando..." so na primeira vez: `dados === undefined && erro === undefined`.

### Armadilhas encontradas
- `loading = carregadoPara !== id` trava com id `NaN` (`parseInt` de rota invalida) — usar
  `!Object.is(carregadoPara, id)`.
- Ao remover um `useState<T>`, o tipo `T` pode virar import sem uso — passar como generico
  `useCarregar<T>(...)`.
- `x ?? []` num valor usado em deps de `useMemo`/`useEffect` gera array novo a cada render —
  envolver em `useMemo`.
- Handler que passa a funcao direto (`onClick={load}`) manda o evento como argumento: com
  `load(p)` de paginacao isso viraria `setPage(evento)`. Conferir todos os usos sem parenteses.

## Relacionado
- [[BTech.Web]]
- [[next-16-react-19]]
