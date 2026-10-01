# Todo erro meu, e como eu o encontrei

Ordem cronológica. O que me interessa em cada um não é o conserto — é **o que me mostrou o
erro**, porque isso é o que se generaliza.

## E-01 · Eu escondi a mensagem de erro com o meu próprio `grep`

Rodei `cargo add` antes de existir `src/lib.rs`, então o Cargo recusou ("no targets specified
in the manifest"). Mas eu havia canalizado a saída para `grep -E "^ *Adding"` para enxugar o
ruído, e o `grep` comeu a mensagem: a célula voltou **vazia**, não vermelha.

Pior: quando reescrevi com `|| echo "FALHOU: $d"`, oito pacotes falharam e eu ainda não vi por
quê. A causa real de um deles era `reqwest --features rustls-tls` — a feature chama-se `rustls`
nesta versão — e **a mensagem que eu estava descartando continha a lista de features válidas**.

**Como achei:** rodando um dos comandos sem o filtro.
**A lição:** filtrar a saída de um comando que pode falhar é apagar a prova. Filtre o sucesso,
nunca o erro.

## E-02 · Três `assert` meus errados em sequência, sobre o mesmo patch

Estava trocando um trecho feio de Rust por script Python, com `assert` para conferir. Falhou
três vezes — e as três foram o *assert*, não o patch:

1. `assert "then_some" not in s` — mas `then_some` aparece legitimamente em `visao`.
2. `assert s.count("then_some") == 2` — chutei 2; era 1 (o outro estava noutro arquivo).
3. Só então percebi que estava validando contra um palpite, não contra um invariante.

**Como achei:** o terceiro erro me fez notar que eu adivinhava o número em vez de derivá-lo.
**A lição:** verificação inventada na hora é mais frágil que o código que verifica. Parei de
adivinhar e deixei o compilador ser a verificação, que era quem tinha autoridade ali.

## E-03 · Escolhi uma porta sem conferir, e culpei meu código pelo nginx de outro

Subi o servidor em `:8099` e o `curl` devolveu `404` com corpo `nginx/1.31.0`. Passei a olhar
minhas rotas. Não era minha: `lsof` mostrou `com.docker.backend` publicando `127.0.0.1:8099`
de um container alheio. Meu bind em `0.0.0.0:8099` **coexistiu** com o `127.0.0.1:8099` dele no
macOS, então meu processo subiu sem erro e o `curl` para loopback foi para o outro serviço.

**Como achei:** `lsof -nP -iTCP:8099 -sTCP:LISTEN`, depois de estranhar a assinatura do nginx
numa resposta que deveria ser minha.
**A lição:** um `404` que vem com o nome de outro servidor não é bug seu. E o teste de
integração passou a usar **porta 0** — o sistema escolhe uma livre, e a classe inteira morre.

## E-04 e E-05 · O impasse do WebSocket, diagnosticado errado uma vez

O teste de integração travava 20 s e morria com "ws calou antes do prazo". Diagnostiquei duas
vezes:

**E-04 (parcial, certo mas insuficiente):** o bot lia um frame por vez e podia agir sobre
estado velho. Drenei a fila antes de decidir. Continuou travando.

**E-05 (a causa):** `estado_ate` **bloqueava esperando frame novo**. Depois da fase de
asserções, as duas filas estavam vazias e o jogo esperava exatamente que a `ana` jogasse. A
`ana` bloqueava esperando um frame que ninguém tinha motivo para mandar — **ela era a causa do
silêncio que estava esperando**.

**Como achei:** parei de olhar a rede e perguntei "quem deveria mandar o próximo frame?". A
resposta era "quem está esperando".
**A lição, que é a melhor deste projeto:** um WebSocket é um **fluxo de eventos**, mas um jogo
é uma **máquina de estados**. Um cliente real guarda o estado renderizado e age sobre ele; meu
teste só sabia esperar o próximo evento. Teste que não imita a estrutura do cliente mede um
cliente que ninguém escreveria. Depois do conserto a suíte caiu de 21 s para 6 s — os 15 s
eram espera em timeout.

## E-06 · Orçamento de iteração não é orçamento de tempo

O e2e falhou com "a partida não terminou em 400 lances". O laço estava certo; o orçamento,
não. O custo dominante de uma partida não são os lances: é a **pausa de 2,6 s entre mãos**,
que existe para o jogador ver o resultado. Cada pausa queima ~43 iterações sem nada a fazer, e
uma partida de 1x1 leva ~12 mãos — as pausas sozinhas consumiam o orçamento todo.

**Como achei:** multipliquei 12 mãos × 2,6 s e comparei com 400 × 60 ms.
**A lição:** quando o laço passa a maior parte do tempo esperando, conte tempo, não voltas.

## E-07 · Asserção com a palavra que os dois desfechos compartilham

`expect(venceA !== venceB)` falhou com os avisos `"a partida acabou — eles venceram"` e
`"🏆 vocês venceram a partida!"`. O jogo estava **coerente**; meu `includes('venceram')` dava
`true` nas duas telas, porque as duas contêm a palavra.

**Como achei:** a própria mensagem de falha imprimia os dois textos lado a lado.
**A lição:** asserção tem de casar com o que **distingue** os desfechos, não com o que eles têm
em comum. Passei a casar `'vocês venceram'` e acrescentei a verificação do lado perdedor.

## E-08 · O bug de verdade, e não fui eu que achei

**O erro no produto:** `entrar()` conferia saldo **antes** de verificar se o jogador já estava
sentado. Voltar para a própria mesa não é aposta nova — ela foi paga quando a mesa encheu —
mas o código cobrava de novo. E com aposta acima da metade do saldo (`1000 − aposta < aposta`)
a conta nunca fechava: o jogador recebia "saldo insuficiente" **justamente porque o saldo dele
já estava descontado do valor exigido**. Quem passasse pelo lobby no meio da partida ficava
trancado fora do próprio jogo, para sempre.

**Como achei: eu não achei.** Achou o teste de front, e por acidente de outra correção. Eu
havia trocado aposta fixa por **aposta aleatória** para isolar testes que compartilham
servidor (a aposta faz parte da chave de pareamento). Com a aposta fixa de 60, `940 > 60`
passava sempre e o bug era invisível às minhas 47 asserções. A aleatoriedade sorteou 639,
`361 < 639`, e o jogador ficou no lobby lendo o erro.

O diagnóstico foi rápido por um segundo acidente de sorte: o Playwright salva o **snapshot da
página** no fracasso, e a tela congelada *dizia* a causa — `saldo insuficiente`, saldo 361,
aposta 639. Não precisei reproduzir nada.

**As duas lições, e acho que são as mais valiosas do projeto:**
1. **Variar o que parece não importar acha bugs que asserção não acha.** Eu tinha 47 testes e
   nenhum pegava isto, porque todos usavam um valor que funcionava. Constante em teste é uma
   hipótese não declarada.
2. **Teste que preserva estado no fracasso vale mais que teste que só diz que falhou.** O
   snapshot do Playwright substituiu uma sessão de depuração inteira — e isso, retroativamente,
   é um argumento por D-12 que eu não tinha quando escolhi a ferramenta.

## E-09 · Meu teste de isolamento tropeçou na minha própria decoração

O teste "nenhuma carta de b aparece no DOM de a" começou a falhar com "a carta 🃑 de b vazou".
Não vazou: o cabeçalho da página é `<h1>🃑 Truco Paulista</h1>`, e eu varria
`page.content()` — a página inteira, logotipo incluído.

**Como achei:** a carta acusada era sempre o ás de paus, que é exatamente o meu logotipo.
Padrão suspeito demais para ser vazamento.
**A lição:** teste de isolamento tem de olhar a superfície onde a informação **significaria**
algo, não todo byte da página. Escopei a `#tela-mesa` e acrescentei o **contrapositivo** — as
cartas de `a` *estão* na mesa de `a` — para a asserção não poder passar por vacuidade. Essa
parte faltava desde o começo: uma verificação de ausência sem verificação de presença pode
estar medindo nada e parecer verde.

## Padrão que atravessa os nove

Dos nove, **cinco** (E-01, E-02, E-06, E-07, E-09) são erros na **verificação**, não no
produto. Um só (E-08) é bug de produto — e foi encontrado por aleatoriedade, não por
asserção. Isso inverte a intuição com que comecei: eu esperava gastar o esforço acertando o
truco e gastei a maior parte acertando **como eu olhava** para o truco.

A pesquisa antes do código é o que explica a assimetria. Com as regras já conferidas e
tabeladas em `regras/truco-paulista.md`, o motor passou 31/31 de primeira (ver
`erros/calibracao.md`). O que não tinha especificação prévia — meus testes — foi onde eu errei.
