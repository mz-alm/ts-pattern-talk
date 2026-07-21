---
theme: seriph
colorSchema: light
title: Pattern Matching (…e ts-pattern)
info: |
  Pattern matching e ts-pattern — meetup Codecon (devs em geral), pt-BR.
  Roteiro completo em roteiro.md.
highlighter: shiki
lineNumbers: false
transition: slide-left
drawings:
  persist: false
mdc: true

layout: center
class: text-center text-xxl
---

## Testando... <v-click>3</v-click> <v-click>2</v-click> <v-click>1</v-click>

---
layout: center
class: text-center
---

# Pattern Matching

uma ideia de 50 anos que talvez você use sem saber

<div class="pt-12 opacity-70">
  Mizael Almeida · Codecon Curitiba · 21/07/2026
</div>

---

# oi, eu sou o Mizael

<div class="flex items-center gap-10 mt-10">

<div class="w-52 h-52 rounded-2xl bg-cover bg-center shrink-0 shadow-xl" style="background-image:url(/me.png)"></div>

<div class="text-lg leading-relaxed">

**Product Engineer** na Salvy · vivo de TypeScript

<div class="mt-6 opacity-80">

github.com/mz-alm<br>
linkedin.com/in/mzalmeida

</div>

</div>

</div>

---

# Antes de tudo: o vocabulário

<div class="text-base mt-4 space-y-2">

- **Pattern (padrão)** — uma *forma* contra a qual você compara um valor
- **Matching** — testar o valor contra os padrões, em ordem; agir no primeiro que casar
- **Exaustividade** — cobrir *todos* os casos possíveis (e o compilador conferir)
- **Tipo algébrico (ADT) / união** — um tipo feito de variantes: `A | B | C`
- **Estreitamento (*narrowing*)** — dentro de um ramo, o tipo fica mais específico
- **Guard** — uma condição extra além da forma (ex.: *"…e se for > 10"*)

</div>

---

# O que é pattern matching?

<div class="mt-6 text-xl leading-relaxed">

Um pattern…

<v-clicks>

- **testa** — *"tem essa forma?"*
- **extrai** — *"…e me dá as peças"*
- pode ser um **literal** — `"admin"`, `42`
- …ou um **tipo** — `P.string`, `P.number`
- …ou uma **estrutura** — `{ status: "error" }`
- e o `match` inteiro é uma **expressão** — retorna valor

</v-clicks>

</div>

<v-click>

<div class="mt-8 border-l-4 border-teal-400 pl-4 text-2xl">
um <code>if</code> pergunta <b>sim/não</b>; um pattern pergunta <b>"que forma é essa — e me dá as peças"</b>. É o <b>imperativo</b> vs. <b>declarativo</b>.
</div>

</v-click>

---

# A herança

<div class="grid grid-cols-1 gap-2 text-xl mt-6">

- **1973** — Hope, ML → matching como construção central
- **Haskell, OCaml** → matching + tipos algébricos
- **Rust** — `match`, exaustividade **obrigatória**
- **Elixir / Erlang** — o operador <code>=</code> não é atribuição, é pattern matching
- **Scala · Swift · Kotlin · C#** → todas adotaram

</div>

---

# Glamour shot: Rust

```rust {all|2-4|5}
match estado {
    Loading            => spinner(),
    Error { error }    => caixa_erro(error),
    Success { data }   => lista(data),
    // faltou um caso? → NÃO COMPILA.
}
```

---

# Todo mundo já faz isso…

<div class="relative w-full h-[470px] mt-2 text-xs">

<div v-click="1" class="absolute top-[0px] left-[0px] -rotate-3 z-[10] shadow-2xl rounded-lg overflow-hidden">

```scala
// Scala
res match
  case Ok(v) if v > 0 => use(v)
  case Err(m)         => log(m)
```

</div>

<div v-click="2" class="absolute top-[20px] left-[330px] rotate-3 z-[20] shadow-2xl rounded-lg overflow-hidden">

```elixir
# Elixir
case fetch(id) do
  {:ok, u}    -> render(u)
  {:error, e} -> flash(e)
end
```

</div>

<div v-click="3" class="absolute top-[10px] left-[650px] rotate-2 z-[30] shadow-2xl rounded-lg overflow-hidden">

```haskell
-- Haskell
describe Nothing  = "—"
describe (Just 0) = "zero"
describe (Just n) = show n
```

</div>

<div v-click="4" class="absolute top-[140px] left-[60px] -rotate-6 z-[40] shadow-2xl rounded-lg overflow-hidden">

```ocaml
(* OCaml *)
match x with
| Some n -> n
| None   -> 0
```

</div>

<div v-click="5" class="absolute top-[120px] left-[370px] -rotate-2 z-[50] shadow-2xl rounded-lg overflow-hidden">

```swift
// Swift
switch result {
case .success(let d): show(d)
case .failure(let e): alert(e)
}
```

</div>

<div v-click="6" class="absolute top-[110px] left-[690px] rotate-6 z-[60] shadow-2xl rounded-lg overflow-hidden">

```kotlin
// Kotlin
when (s) {
  is Ok  -> render(s.data)
  is Err -> log(s.msg)
}
```

</div>

<div v-click="7" class="absolute top-[260px] left-[20px] rotate-1 z-[70] shadow-2xl rounded-lg overflow-hidden">

```csharp
// C#
area = shape switch {
  Circle c => PI * c.R * c.R,
  Square s => s.Side * s.Side,
};
```

</div>

<div v-click="8" class="absolute top-[250px] left-[370px] -rotate-3 z-[80] shadow-2xl rounded-lg overflow-hidden">

```fsharp
// F#
match opt with
| Some x -> x
| None   -> 0
```

</div>

<div v-click="9" class="absolute top-[260px] left-[660px] rotate-3 z-[90] shadow-2xl rounded-lg overflow-hidden">

```dart
// Dart
switch (shape) {
  Circle(:var r) => pi*r*r,
  Square(:var s) => s*s,
}
```

</div>

<div v-click="10" class="absolute top-[170px] left-[380px] rotate-1 z-[100] scale-150 shadow-2xl rounded-lg border-2 border-dashed border-yellow-400">

```js
// JavaScript
// ...🦗
```

</div>

</div>

---

# O JS quer isso — TC39

<div class="text-xl mt-8">

Proposta oficial: **TC39 Pattern Matching**

`match` nativo com cláusulas `when` &nbsp;·&nbsp; **Stage 1** = anos de espera

```js
return match (res) {
  when { status: 200, let body, ...let rest }: handleData(body, rest);
  when { const status, destination: let url } and if (300 <= status && status < 400):
    handleRedirect(url);
  when { status: 500 } and if (!this.hasRetried): do {
    retry(req);
    this.hasRetried = true;
  };
  default: throwSomething();
}
```

</div>

<div class="mt-10 border-l-4 border-teal-400 pl-4 opacity-85">
Ou seja: o problema é real e <b>reconhecido</b> — mas nativo, só daqui a uns anos (se isso).
</div>

---

# Você já faz isso <v-click>(mal)</v-click>

<div v-click=1 class="grid grid-cols-2 gap-4">

<div>

```ts
// num reducer / máquina de estados
switch (action.type) {
  case "increment":
    return { ...s, n: s.n + 1 };
  case "decrement":
    return { ...s, n: s.n - 1 };
  default:
    return s; // engole casos novos
}
```

</div>

<div>

```ts
// …e ao tratar um resultado, na unha:
if (res.status === "ok") {
  if (res.data) return handle(res.data);
  return empty();
} else if (res.status === "error") {
  return fail(res.error);
}
return retry(); // e os cruzamentos?
```

</div>

</div>

---

# Por que o jeito atual dói

<div class="text-xl mt-6 space-y-3">

- `switch` só compara **uma** expressão, com `===` → não correlaciona campos, não olha dentro
- condicional aninhada (if/ternário) → ilegível com 3+ casos
- **sem exaustividade** → `default` / `: null` esconde caso faltando
- você adiciona um estado novo → **compila, roda, quebra em prod**

</div>

---

# Você já tem a metade difícil

```ts
type Estado =
  | { status: "loading" }
  | { status: "error";   error: Error }
  | { status: "success"; data: Item[] };
```

<div class="mt-6 opacity-85">
Isso é um <b>tipo algébrico</b> — exatamente a forma que pattern matching consome.<br>
O TS já te dá a estrutura. Falta a ferramenta pra <b>ler</b> ela com segurança.
</div>

---

# `ts-pattern`

<div class="text-xl mt-4">Gabriel Vergnaud · zero-dep · tree-shakeable · type-safe de verdade</div>

```bash
npm i ts-pattern
```

<div v-click class="mt-8 border-l-4 border-amber-400 pl-4">
<b>Confissão:</b> eu coloquei isso no nosso codebase.<br>
Hoje (front + API): <b>86 arquivos</b>, <b>98 <code>match</code></b>, <b>37 com <code>.exhaustive()</code></b>. Bora ver se valeu.
</div>

<v-click>

<div class="mt-8 text-center text-lg italic opacity-75">
🎬 Todos os exemplos a seguir são <b>baseados em fatos reais.</b>
</div>

</v-click>

---

# Degrau 1: união de strings (on-ramp)

<div class="text-sm opacity-50 -mt-2">real: prefixo de ICCID por operadora</div>

```ts {1-6|8-11|12}
// ANTES — switch, e o default aceita operadora nova calado
switch (operadora) {
  case "operadora-a": return prefixoA(iccidSuffix);
  case "operadora-b": return prefixoB(iccidSuffix);
  default: throw new Error("operadora sem formato de ICCID");
}

// DEPOIS — o caso mais simples possível de match
match(operadora)
  .with("operadora-a", () => prefixoA(iccidSuffix))
  .with("operadora-b", () => prefixoB(iccidSuffix))
  .exhaustive(); // ✅ some uma operadora nova na union → NÃO COMPILA
```

---

# Degrau 2: combinação de booleans

<div class="text-sm opacity-50 -mt-2">real: CheckoutStepReviewCard</div>

```tsx
// 2 booleans, 3 resultados — o inferno do if aninhado:
let descricao;
if (isWhatsApp) {
  if (isUnlimited) {
    descricao = "whatsapp ilimitado";
  } else {
    descricao = "whatsapp";
  }
} else {
  descricao = "linha comum";
}
```

<v-click>

```tsx
// …e a mesma coisa como uma tabela plana:
const descricao = match({ isWhatsApp, isUnlimited })
  .with({ isWhatsApp: true,  isUnlimited: false }, () => "whatsapp")
  .with({ isWhatsApp: true,  isUnlimited: true  }, () => "whatsapp ilimitado")
  .with({ isWhatsApp: false }, () => "linha comum") // isUnlimited nem importa
  .exhaustive();
```

</v-click>

---

# Degrau 3: casar pela *forma* (união discriminada)

<div class="text-sm opacity-50 -mt-2">real: roteamento de conta de telefone</div>

```ts
// dois tipos de linha, cada um com seu repositório:
const conta = await match(linha)
  .with({ tipo: "interna" }, (l) => contasInternas.findById(l.id))
  .with({ tipo: "externa" }, (l) => contasExternas.getById(l.id))
  .exhaustive();
```

<v-click>

```ts
// bônus: normalizar dado bagunçado de fora (snake_case OU camelCase)
const numero = match(conta)
  .with({ phone_number: P.nonNullable }, (c) => c.phone_number)
  .with({ phoneNumber:  P.nonNullable }, (c) => c.phoneNumber)
  .otherwise(() => null);
```

</v-click>

---

# ⭐ A estrela: união discriminada + `.exhaustive()`

<div class="text-sm opacity-50 -mt-2">real: timeline de eventos de uma linha</div>

```ts {all|1-5|7-12|9-10|13}
type Evento =
  | { nome: "criada" }
  | { nome: "ativada"; simType: "fisico" | "esim" }
  | { nome: "recarga"; gb: number }
  | { nome: "bloqueada"; motivo: string };

const label = match(evento)
  .with({ nome: "criada" },                     () => "criou a linha")
  .with({ nome: "ativada", simType: "fisico" }, () => "ativou (chip físico)")
  .with({ nome: "ativada" },                    () => "ativou a linha")
  .with({ nome: "recarga" },                    (evt) => `+${evt.gb} GB`)           // evt.gb: number ✅
  .with({ nome: "bloqueada" },                  (evt) => `bloqueou: ${evt.motivo}`) // evt.motivo: string ✅
  .exhaustive();
```

<div v-click class="text-red-400 text-sm mt-2 font-mono">
❌ adiciona <code>{ nome: "cancelada" }</code> na union → <code>TS2349: This expression is not callable. Type 'NonExhaustiveError&lt;{ nome: "cancelada" }&gt;' has no call signatures.</code>
</div>

---
layout: center
class: text-center
---

# a demo, gravada

<video controls muted playsinline :src="'/demo-exhaustive.mp4'" class="mx-auto mt-4 max-h-[420px] rounded-lg shadow-2xl"></video>

---

# O callback recebe o tipo **estreitado**

<div class="text-sm opacity-50 -mt-2">…e você monta o retorno de cada caso com ele</div>

```ts {all|2-6|7-11|12-16}
const item = match(evento)                     // Mesmo tipo Evento de antes
  .with({ nome: "recarga" }, (evt) => ({       // evt: { nome:"recarga"; gb:number }
    icone: Plus,
    titulo: `+${evt.gb} GB`,                   // evt.gb: number ✅
    cor: "green",
  }))
  .with({ nome: "ativada" }, (evt) => ({       // evt: { nome:"ativada"; simType:… }
    icone: Power,
    titulo: "Linha ativada",
    detalhe: evt.simType === "fisico" ? "chip físico" : "eSIM", // ignore o ternário 🙄
  }))
  .with({ nome: "bloqueada" }, (evt) => ({     // evt: { nome:"bloqueada"; motivo:string }
    icone: Lock,
    titulo: "Linha bloqueada",
    detalhe: evt.motivo,                       // evt.motivo: string ✅
  }))
  .exhaustive();
```

---

# O catálogo: `P`

```ts {all|1|4|5|6|7|8|9}
import { match, P } from "ts-pattern";

match(input)
  .with(P.string,                                (s) => s.trim())               // P no TOPO — não precisa de objeto
  .with([P.string, P.number],                    ([nome, n]) => `${nome}:${n}`) // numa tupla
  .with({ role: "admin", id: P.number },         () => adminView())             // dentro de um objeto
  .with({ items: P.array({ price: P.number }) }, () => render())                // aninhado lá no fundo
  .with({ status: P.union("failed", "late") },   () => alerta())                // vários valores
  .with({ tipo: "log", payload: P._ },           () => manter())                // P._ = qualquer valor naquele campo
```

---

# Como o `match` termina

<div class="text-lg mt-6 space-y-4">

<v-clicks>

- **`.exhaustive()`** — exige que **todos** os casos estejam cobertos; faltou um → **erro de build**. ✅ *seguro*
- **`.otherwise(() => …)`** — um fallback pra tudo que sobrou (o "default"). Sem prova em compile-time, mas cobre.
- **`.run()`** — roda e **estoura em runtime** se nada casar. Sem rede de tipos. ⚠️ *"use at your own risks"*

</v-clicks>

</div>

---

# Guards e seleção: `P.when`, `P.select`

<div class="text-sm opacity-50 -mt-2">real: P.when do repo</div>

```ts{all|1-8|1-2|4-5|7-8|10-11}
// 1. P.when — um guard que VIRA pattern (type guard real, do nosso código)
.with({ icon: P.when((i): i is IconComponent => typeof i === "function") }, ({ icon }) => render(icon))

// 2. match().when(guard, handler) — guard no nível do match, sem pattern
.when((x) => x.total > x.limite, () => bloquear())

// 3. .with(pattern, guard, handler) — o "case X if cond" do Scala, aqui
.with({ amount: P.number }, ({ amount }) => amount > 10_000, () => revisar())

// P.select — extrai só o pedaço que casou, sem desestruturar na mão
.with({ type: "move", to: P.select() }, (to) => mover(to))
```

---

# Honestidade: o que adotamos e o que dormimos no ponto

<div class="text-xl mt-4 space-y-2">

<v-click>

- ✅ `.exhaustive()` em **37 arquivos** — pegamos o espírito

</v-click>
<v-click>

- ⚠️ ainda tem **1** `.run()` (terminal **unsafe** — não checa em compile-time) onde devia ser `.exhaustive()`

</v-click>
<v-click>

- 😴 `isMatching` e `P.select`: **zero** usos

</v-click>
<v-click>

O `isMatching` coopera com o `if`, em vez de competir

```ts
// isMatching: um PATTERN dentro de um if / filter — o seu "if" com superpoder
const myPattern = { status: "active", plano: { tipo: "ilimitado" } };

if (isMatching(myPattern, value)) { // ou isMatching(myPattern)(value) -> curry 🍛
  // ...
}
// curry 🍛 usado direto no parâmetro do filter
linhas.filter(isMatching(myPattern))
```
</v-click>

</div>

---

# 🍒 Um caso real — semana passada

<div class="text-sm opacity-50 -mt-2">PR do Otte · emissão de nota fiscal</div>

```ts {all|1-6|8-14|13}
// ANTES — um switch sobre o status da nota
switch (resultado.status) {
  case "approved":   return aprovar();
  case "rejected":   return rejeitar();
  case "processing": return esperar();
}  // o contrato ganhou "not-found" depois — o switch nem percebe

// DEPOIS — o mesmo, virado match
match(resultado)
  .with({ status: "approved" },   aprovar)
  .with({ status: "rejected" },   rejeitar)
  .with({ status: "processing" }, esperar)
  .with({ status: "not-found" },  recuperar)   // ← o compilador EXIGIU este
  .exhaustive();
```

<div v-click class="mt-4 border-l-4 border-teal-400 pl-4">
Mesmo código, de <code>switch</code> pra <code>match(…).exhaustive()</code> — e agora, quando o contrato ganha um status novo, o compilador <b>obriga</b> todo consumidor a tratar. Não dá mais pra esquecer um caso.
</div>

<div v-click class="mt-4 text-teal-300 border-l-4 border-teal-400 pl-4">
🤖 E esse PR? Escrito <b>com IA</b>. É <i>exatamente</i> por isso que o <code>.exhaustive()</code> importa:
a máquina escreveu, o compilador garantiu. Ele não liga se foi você ou a IA que digitou.
</div>

---

# Quando NÃO usar

<div class="text-xl mt-6 space-y-3">

<v-clicks>

- **+~12kb** no bundle (tree-shakeable, mas existe)
- custo de runtime vs. `switch` puro → ruído em UI; em hot loop, **mede — não chuta**
- curva pra quem nunca viu matching
- `if` de um caso só? Continua sendo um `if`. Não force.

</v-clicks>

</div>

---

# Leva pra casa

<div class="text-lg mt-4 space-y-2">

1. Você **já faz** pattern matching — em todo reducer e ternário aninhado
2. ideia de 50 anos; JS ainda não tem nativo (TC39 stage 1)
3. **ts-pattern** entrega hoje, type-safe — brilha em **objeto / multi-campo / união discriminada**
4. `.exhaustive()` transforma "esqueci um caso" em **erro de compilação**
5. valeu a pena no nosso codebase? **valeu** — 98 `match` e contando

</div>

---

<div class="relative w-full h-[480px]">

<div class="absolute bottom-0 left-1/2 -translate-x-1/2 w-[440px] h-[330px] z-10 pointer-events-none bg-contain bg-no-repeat bg-bottom" style="background-image:url(/trashcan_back.png)"></div>

<div v-click class="absolute left-[44%] -translate-x-1/2 bottom-[260px] origin-bottom -rotate-[290deg] z-20 shadow-xl rounded-md overflow-hidden text-[0.5rem] leading-tight">

```ts
switch (true) {
  case x > 10: return a;
  case y < 5:  return b;
}
```

</div>

<div v-click class="absolute left-[55%] -translate-x-1/2 bottom-[330px] origin-bottom rotate-[110deg] z-20 shadow-xl rounded-md overflow-hidden text-[0.5rem] leading-tight">

```ts
a ? (b ? c : d) : e
```

</div>

<div v-click class="absolute left-1/2 -translate-x-1/2 bottom-[275px] origin-bottom rotate-[22deg] z-20 shadow-xl rounded-md overflow-hidden text-[0.5rem] leading-tight">

```ts
if(a){ if(b){ if(c){} } }
```

</div>

<div class="absolute bottom-0 left-1/2 -translate-x-1/2 w-[440px] h-[330px] z-30 pointer-events-none bg-contain bg-no-repeat bg-bottom" style="background-image:url(/trashcan_front.png)"></div>

<div v-click class="absolute left-1/2 -translate-x-1/2 bottom-[190px] text-7xl text-center animate-pulse z-40 select-none">🔥🔥🔥<br>🔥🔥</div>

</div>

<v-click>
<div class="text-center text-xl italic opacity-80 -mt-2">Nenhum <code>switch</code> foi maltratado na produção desta talk. 🔥</div>
</v-click>

---
layout: center
class: text-center
---

# Obrigado! 🙏

### Perguntas?

<div class="flex flex-col items-center mt-6">
  <div class="bg-white p-3 rounded-xl shadow-xl">
    <div class="w-48 h-48 bg-contain bg-no-repeat bg-center" style="background-image:url(/qr_code.png)"></div>
  </div>
  <div class="text-sm opacity-70 mt-3">📱 slides, links e tudo mais ☝️</div>
</div>

<div class="mt-8 opacity-80 text-base">
github.com/gvergnaud/ts-pattern &nbsp;·&nbsp; tc39/proposal-pattern-matching<br>
github.com/mz-alm &nbsp;·&nbsp; linkedin.com/in/mzalmeida &nbsp;·&nbsp; github.com/mz-alm/ts-pattern-talk
</div>

<div class="mt-6 text-xs opacity-40">feito com Slidev · powered by Vue</div>

