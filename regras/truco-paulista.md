# Regras do truco paulista — especificação executável

Cada regra tem ID. `R-` = vem de fonte citada. `D-` = decisão minha, porque nenhuma fonte
cobre. O código do repositório operacional cita o ID no comentário.

## R-01 · Baralho

40 cartas: naipes completos sem `8`, `9`, `10` e sem curinga.
> "ele é disputado com um baralho especial, que não tem as seguintes cartas: '8', '9', '10' e curinga" — F-01

## R-02 · Ordem de força das cartas comuns

Da mais fraca para a mais forte: `4 < 5 < 6 < 7 < Q < J < K < A < 2 < 3`.
> "O valor das cartas, do maior para o menor é 3, 2, A, K, J, Q, 7, 6, 5, 4" — F-01
> "4 < 5 < 6 < 7 < Q (Dama) < J (Valete) < K (Rei) < Ás < 2 < 3" — F-02

Duas fontes independentes concordam. Note a inversão contraintuitiva: **Q é mais fraca que J**.
> "a 'Q' é mais fraca que o 'J'" — F-01

**Entre cartas comuns o naipe não desempata** — duas cartas de mesmo valor empatam a rodada.

## R-03 · Vira e manilha

Depois de distribuir, vira-se uma carta (a *vira*). A manilha é o valor **seguinte** à vira na
ordem de R-02, nos quatro naipes.
> "A manilha é a carta que vem na sequência da vira. Por exemplo, se uma carta '5' for a vira
> da rodada, as manilhas serão os '6'." — F-01

A ordem é **circular**: vira `3` ⇒ manilha `4`.
> "Atenção: quando a vira for o '3', as manilhas são as cartas '4'." — F-01

Isto é o que impede tabela de força fixa: a força é função de `(carta, vira)`.

## R-04 · Força entre manilhas (o naipe desempata)

`Ouros < Espadas < Copas < Paus`.
> "Dentre elas, a ordem de força obedece o naipe, da seguinte maneira (do maior para o menor):
> Paus > Copas > Espadas > Ouros" — F-01
> "Ouros < Espadas < Copas < Paus" — F-02

Logo **duas manilhas nunca empatam** — só cartas comuns empatam.

## R-05 · Estrutura da mão

Três cartas por jogador, mão disputada em melhor de três rodadas; a carta mais forte leva a
rodada; quem leva duas rodadas leva a mão.
> "Cada mão de truco tem três rodadas" / "Quem levar duas das três rodadas ganha a mão" — F-01

## R-06 · Escada de pontos

| Pedido | Aceito, a mão vale | Corrido, quem pediu ganha |
|---|---|---|
| (nenhum) | 1 | — |
| Truco | **3** | 1 |
| Seis | 6 | 3 |
| Nove | 9 | 6 |
| Doze | 12 | 9 |

> tabela textual em F-02. Correr concede sempre o **valor anterior** da mão.

F-01 confirma a escada `1 → 3 → 6 → 9 → 12` ("esse valor pode ser aumentado para 3, 6, 9 e até
12 pontos") e os valores de corrida (nove corrido = 6, doze corrido = 9), mas erra o truco
aceito — ver `refutado/R-01`.

## R-07 · Quem pode pedir, e quando

Só na própria vez, antes de jogar a carta.
> "Truco — É um pedido de aumento de aposta que só pode ser feito na vez do jogador." — F-01
> "O jogador pode trucar somente na sua vez, antes de jogar." — F-04

O re-aumento (seis/nove/doze) só pode ser pedido por quem **acaba de ser desafiado** — é
resposta, não iniciativa. Quem pediu não pode subir sozinho.
> "Seis — É um pedido de aumento da aposta que pode ser feito quando um jogador é desafiado
> com o pedido de Truco." — F-01

Ver `decisoes/D-02` para o conflito com F-03.

## R-08 · Empate

| Situação | Quem leva a mão |
|---|---|
| Empata a 1ª | vencedor da 2ª |
| Empata a 2ª | vencedor da 1ª |
| Empata 1ª e 2ª | vencedor da 3ª |
| Empata a 3ª | vencedor da 1ª |
| Empatam as três | ninguém pontua |

> os cinco critérios literais em F-01, seção "Empate".

Consequência não óbvia que o código precisa respeitar: "empata a 2ª ⇒ vencedor da 1ª leva" faz
a mão **terminar na 2ª rodada** — não se joga a 3ª. O mesmo para "empata a 1ª e alguém leva a
2ª"? Não: aí ainda falta decidir, porque empate na 1ª + vitória na 2ª já define o vencedor, a
mão termina. A 3ª só é jogada quando ninguém tem duas rodadas nem critério de empate resolvido.

## R-09 · Objetivo

Vence quem fizer **12 pontos**.
> "A dupla que fizer 12 pontos, ganha a partida." — F-01

## R-10 · Mão de onze

Quando uma dupla chega a 11 pontos, a mão seguinte vale **3 pontos** e a dupla de 11 vê as
cartas do parceiro e escolhe jogar ou correr; se correr, os adversários ganham **1 ponto**.
> "disputada quando uma dupla chega a 11 pontos (...) têm o direito de olhar as cartas do
> parceiro e decidir se querem jogar ou correr. Caso a dupla aceite, a Mão de Onze vale três
> pontos. Mas se a dupla correr, a dupla adversária ganha um ponto." — F-01

Nenhum aumento é permitido nesta mão.
> "Quem está em mão de 11 não pode pedir Truco nem qualquer aumento. Pedir = derrota
> imediata." — F-02

## R-11 · Mão de ferro (11 a 11)

Com as duas duplas em 11, joga-se às cegas: ninguém vê carta de parceiro, ninguém corre, a mão
vale **3 pontos** e decide a partida.
> "Se as duas duplas estão com 11 pontos (11 a 11), joga-se a mão de ferro: ninguém olha as
> cartas e a mão vale 3 pontos." — F-03
> "Mão de Ferro - É a Mão de Onze especial, quando as duas duplas conseguem chegar a 11
> pontos na partida." — F-01 (confirma a existência, não o valor)

## R-12 · Carta encoberta

Pode-se jogar uma carta virada, que não vale nada — **proibido na primeira rodada**.
> "Você pode jogar uma carta virada na mesa, que passará a não valer nada. (...) Lembre-se que
> não é permitido esconder a carta na primeira rodada de cada mão." — F-01

---

# Decisões onde a fonte silencia

## D-01 · Ordem de jogo

O embaralhador roda no sentido horário a cada mão. A primeira rodada é puxada pelo jogador à
esquerda do embaralhador (o *mão*); o último a jogar é o *pé*. Rodadas seguintes são puxadas
pelo vencedor da rodada anterior.
Base: F-03 define *mão* como "o primeiro a jogar na rodada" e *pé* como "o último (...) leva
vantagem por jogar sabendo das outras cartas", e F-04 manda distribuir "no sentido horário
iniciando pelo jogador a sua esquerda" — mas nenhuma fecha a regra. Convenção universal de
jogos de vaza.
**Alternativa descartada:** embaralhador fixo. Descartada porque a vantagem do *pé* é
estrutural e um embaralhador fixo a congelaria num jogador.

## D-02 · Quando se pode trucar — conflito entre fontes

F-03 diz "a qualquer momento, uma dupla pode pedir truco". F-01 e F-04 dizem que só na vez do
jogador. **Resolvo por 2-contra-1 e por especificidade**: F-01 e F-04 fazem a afirmação
restritiva de forma direta e normativa; a de F-03 aparece numa frase de resumo. Vale R-07.
**Alternativa descartada:** permitir a qualquer momento. Descartada não só por ser minoria nas
fontes, mas porque num jogo em rede "a qualquer momento" é uma corrida: dois jogadores pedindo
no mesmo milissegundo precisam de desempate arbitrário, e isso é complexidade comprada sem
capacidade.

## D-03 · Truco em 1x1

Nenhuma das quatro fontes descreve 1x1 — F-04 chega a definir participantes como "2 duplas".
O enunciado exige 1x1. Decido: **mesmas regras, dois jogadores, 3 cartas cada, uma vira**. Na
mão de onze de 1x1 o jogador vê a própria mão (não há parceiro) e decide jogar ou correr; o
efeito prático é idêntico. A mão de ferro (11 a 11) é jogada sem decisão, como em R-11.
**Alternativa descartada:** inventar uma variante 1x1 com 4 cartas ou duas viras, por analogia
com outros jogos. Descartada: o enunciado pede truco paulista, e inventar regra onde a fonte
silencia é pior que estender a regra existente pelo caminho mais curto.

## D-04 · Quem responde ao truco, e quem decide a mão de onze

**Responde ao truco o adversário imediatamente à esquerda de quem pediu** — isto é, o próximo
a jogar. Em 1x1 é trivialmente o outro jogador. Em 2x2 os assentos alternam equipe, então
`(pedinte + 1) % n` é sempre um adversário, e é quem jogaria em seguida se não houvesse pedido.
Na mesa física, qualquer um da dupla desafiada pode responder.
**Alternativa descartada:** aceitar a resposta do primeiro da dupla que falar. Descartada pelo
mesmo motivo de D-02: é uma corrida entre dois clientes, exige desempate arbitrário e não
compra capacidade de jogo. Custo que aceito: perde-se o momento social de o parceiro responder.

Pela mesma razão, **quem decide a mão de onze** é um assento determinado, não "a dupla":
escolho o membro da equipe em 11 que puxa a mão (o primeiro dela na ordem de jogo a partir do
puxador). Os dois veem as cartas (é o direito que R-10 concede); um só clica.
**Alternativa descartada:** exigir que os dois concordem. Descartada porque cria um impasse sem
saída quando um deles não responde — e v1 não tem relógio de turno.

## D-05 · Quem puxa depois de um empate

Quem puxou a rodada empatada puxa a seguinte. Nenhuma fonte diz.
**Alternativa descartada:** o *mão* da mão puxar sempre. Descartada por ser incoerente com
D-01 (vencedor puxa) — manter uma única regra "quem levou, ou quem puxou se não houve
vencedor" é mais simples que duas.

## D-06 · Pedir aumento em mão de onze é recusado, não punido

F-02 diz "Pedir = derrota imediata". Implemento como **ação inválida** (`SemAumentoNaMaoEspecial`):
o pedido é recusado e o jogador segue jogando.
**Alternativa descartada:** aplicar a derrota imediata literal. Descartada porque num cliente web
o botão de truco é um pixel ao lado do da carta, e punir com a partida inteira um erro de clique
transforma regra de etiqueta de mesa em perda de 12 pontos. Numa mesa física a punição faz
sentido porque falar é deliberado; num clique, não. O custo que aceito: divirjo de F-02 neste
ponto, e declaro.

## D-07 · A visibilidade da mão de onze fecha com a decisão

R-10 dá à equipe em 11 o direito de "olhar as cartas do parceiro (...) e decidir". Decido que a
visibilidade vale **somente durante a decisão** e fecha quando ela é tomada.
**Alternativa descartada:** manter a mão do parceiro visível pela mão inteira. Descartada porque
a fonte amarra o direito ao ato de decidir, e visibilidade permanente mudaria o jogo bem mais do
que a regra pretende — jogar a mão inteira vendo seis cartas em vez de três é outro jogo.
