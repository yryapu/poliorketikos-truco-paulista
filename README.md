# poliorketikos-truco-paulista

A pesquisa por trás de **[truco-paulista](https://github.com/yryapu/truco-paulista)**: de onde
veio cada regra do jogo, o que eu decidi onde as fontes silenciam, o que previ antes de
verificar, e o que foi refutado.

Este repositório existe separado do código de propósito. **Por quê**, e por que eu concordo com
a separação: pesquisa e código têm *ritmos de refutação* diferentes. Uma regra de truco errada
invalida o código, mas o inverso não vale — refatorar o servidor não muda o que a fonte diz.
Separar dá à pesquisa um histórico próprio, auditável, onde um commit que corrige uma crença
fica visível **como** mudança de crença, em vez de enterrado num diff de 400 linhas de Rust. E
torna o operacional autocontido: ele compila sem ler isto.

O custo que aceito: duas origens, risco de divergirem. Mitigo citando IDs — o código Rust
comenta `R-07` ou `D-03` e aponta para cá. A separação **não** pode virar duas verdades: a
pesquisa é a fonte, o código cita e nunca reinterpreta.

## Por onde começar

| Arquivo | O que tem |
|---|---|
| [`regras/truco-paulista.md`](regras/truco-paulista.md) | **comece aqui.** As 12 regras com trecho textual e fonte (`R-nn`), e as 7 decisões onde as fontes silenciam ou se contradizem (`D-nn`) |
| [`fontes/README.md`](fontes/README.md) | as 4 fontes, com **como** foram obtidas e **quando**, e os 4 buracos que nenhuma cobre |
| [`refutado/R-01-truco-aceito-vale-2.md`](refutado/R-01-truco-aceito-vale-2.md) | o erro no PDF oficial, e como o achei sem precisar de segunda fonte |
| [`erros/registro.md`](erros/registro.md) | **os nove erros meus**, com o que me mostrou cada um |
| [`previsoes/registro.md`](previsoes/registro.md) | as 10 previsões com `p`, resultado e evidência — inclusive a que errei |
| [`decisoes/`](decisoes/) | stack, não-WASM, Playwright, e a medição das ferramentas do harness |

## Os três achados que eu levaria daqui

**1. Contradição interna é mais barata de achar que divergência entre fontes.**
O PDF oficial do Jogatina diz que truco aceito vale **2** pontos. É falso. Não precisei de
segunda fonte para desconfiar: duas frases acima, o mesmo documento declara a escada
`1 → 3 → 6 → 9 → 12`, e declara que correr concede o valor anterior — e nenhuma das duas fecha
com 2. Ler o documento inteiro antes de extrair o número é o que pega isso; ler para extrair um
campo é como se perde. ([`refutado/R-01`](refutado/R-01-truco-aceito-vale-2.md))

**2. Cinco dos meus nove erros foram na *verificação*, não no produto.**
Filtrei com `grep` a saída de um comando que falhou e apaguei a mensagem que continha a
resposta. Orcei um laço em iterações quando o custo era tempo de espera. Assertei com uma
palavra que os dois desfechos compartilhavam. Varri a página inteira num teste de isolamento e
tropecei no meu próprio logotipo. Um só erro foi de produto — e **não fui eu que o achei**:
achou a aposta aleatória que eu tinha introduzido por outro motivo. Isso inverte a intuição com
que comecei. ([`erros/registro.md`](erros/registro.md))

**3. Pesquisar antes de codificar muda a *distribuição de erro* do código, não só evita regra
errada.**
Com as regras já conferidas e tabeladas, o motor de truco passou **31 de 31** testes na
primeira execução. Onde eu não tinha especificação prévia — meus próprios testes — foi onde
errei. Eu havia previsto isso com `p = 0,55`, e devia ter previsto mais alto.
([`erros/calibracao.md`](erros/calibracao.md))

## Sobre as ferramentas do harness

Durante o trabalho, outra sessão me informou que eu havia rodado 72 turnos sem chamar nenhuma
das ferramentas `verbum` disponíveis. [`decisoes/D-13`](decisoes/D-13-ferramentas-do-harness.md)
registra o episódio inteiro, incluindo **um veredito meu que ficou errado e a correção**: eu
concluí que as ferramentas "compraram pouco", e depois uma delas achou dois defeitos de
correção que 52 testes verdes não achavam. Preservo o veredito errado em vez de apagá-lo.

A assimetria é o achado: ferramenta de **consulta** responde o que você pensou em perguntar;
a que comprou valor foi a que **fez pergunta sobre o que eu estava prestes a afirmar**.
