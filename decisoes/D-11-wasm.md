# D-11 · WASM no cliente: **não**. Por quê.

O enunciado permite WASM "se fizer sentido depois de você investigar". Investiguei e a resposta
é não. Três razões, em ordem de peso.

## 1. A razão de arquitetura (decisiva)

O servidor tem de ser **autoritativo sobre as cartas**. Se o cliente conhece o baralho, a vira
ou a mão do adversário, o jogo tem trapaça — e num jogo com saldo apostado, trapaça é o modo de
falha que importa. Logo toda a regra (R-01..R-12) vive no servidor, e o cliente faz duas
coisas: renderizar o estado que chega e enviar quatro intenções (`jogar`, `truco`, `responder`,
`encobrir`).

Isso esvazia o argumento central do WASM. O atrativo de Rust no cliente seria **compartilhar o
crate de regras** entre servidor e navegador. Aqui isso não só é inútil, é **contraindicado**:
mandar o avaliador de força de carta para o cliente convida a duplicar a autoridade, e duas
cópias da regra é a forma clássica de elas divergirem. A fronteira cliente/servidor neste jogo
é exatamente a fronteira de confiança, e ela não deve ser atravessada por código compartilhado.

## 2. A razão medida

O cliente é 100% DOM e 0% CPU: ele desenha ~20 elementos e escuta um socket. É o pior caso para
WASM. Da investigação (`fontes` F-05, F-06):

> "for DOM-heavy code, JavaScript is a better choice because every update of a DOM through WASM
> requires an interop boundary crossing"

> "WASM doesn't give free wins on I/O, DOM access, or business logic (...) if most time is spent
> waiting on networks or databases, WASM won't help"

E o custo não é hipotético: um dashboard de referência sai a **86 KB (Leptos)**, 180 KB (Yew),
210 KB (Dioxus) de wasm gzipado; app Leptos típico com rotas, 180–280 KB gzipado. Meu cliente
inteiro em JS/CSS/HTML é **menor que o piso de qualquer um deles**, e os ganhos de 1,5–3× que o
WASM entrega aparecem em "image processing, compression, simulation" — nada que eu faça.

## 3. A razão de ferramenta

WASM traria `trunk` ou `wasm-pack`, um alvo `wasm32-unknown-unknown`, e um passo de build no
`Dockerfile`. O host desta sessão tem o **`node` quebrado** (`libllhttp.9.3.dylib` ausente), o
que já me empurra para "sem passo de build no front" por motivo independente — e um front sem
build é um front que abre com `file://` e depura no inspector sem source map.

## O único candidato legítimo, e por que ainda não

Se alguma coisa fosse para WASM, seria `forca_da_carta(carta, vira) -> u8` — função pura, sem
I/O, testável, idêntica nos dois lados. Serviria para o cliente **ordenar a mão na tela** sem
pedir ao servidor. São ~30 linhas de Rust para economizar ~10 de JS, e reintroduz a duplicação
da razão 1. Não compra capacidade.

## Cai se

Esta decisão cai se o cliente passar a fazer trabalho de CPU de verdade — animação de física de
cartas, replay local de milhares de mãos para estatística, ou um bot client-side. Nenhum está
no escopo da v1.

## Fontes desta decisão

| ID | O que é | Como obtida | Quando |
|----|---------|-------------|--------|
| F-05 | *WebAssembly for JS Full-Stack, Minus the Hype* | `WebSearch` → resumo citado | 2026-10-01 |
| F-06 | Comparativos de bundle Leptos/Yew/Dioxus 2026 (pistack, rustify, reintech) | `WebSearch` | 2026-10-01 |
