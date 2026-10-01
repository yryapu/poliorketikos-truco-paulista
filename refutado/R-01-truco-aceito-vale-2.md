# Refutado · "Truco aceito faz a mão valer 2 pontos"

**Previsão registrada antes de verificar** (`previsoes/P-001`): o truco aceito vale 3, e o "2"
de F-01 é erro de redação. `p = 0.93`. **Resultado: acertei.**

## O que a fonte dizia

F-01 (PDF oficial do Jogatina), seção Definições:

> "Truco - É um pedido de aumento de aposta que só pode ser feito na vez do jogador. Se nenhum
> jogador pedir Truco, a partida valerá um ponto. Se o Truco for pedido, e o adversário
> **aceitar, a partida passa a valer dois pontos**. Se o adversário não aceitar, o desafiador
> ganha um ponto."

## Como encontrei

Não foi comparando fontes — foi **lendo a própria fonte contra si mesma**. Duas frases acima,
o mesmo documento diz:

> "Mão – Por definição cada mão vale 1 ponto, mas esse valor pode ser aumentado para **3, 6, 9
> e até 12** pontos em função do truco."

Se o truco aceito levasse a mão a 2, o `3` não teria de onde sair: nenhum outro pedido produz 3
(seis→6, nove→9, doze→12). A escada declarada e o valor declarado são inconsistentes. O erro
está no "dois".

## Confirmação externa

F-02 traz tabela explícita: `Truco | 3 pontos | 1 ponto`. F-01 também já é coerente com 3 nos
outros níveis: "Nove (...) Se o adversário não aceitar o pedido, o desafiador ganha seis
pontos" e "Doze (...) o desafiante ganha nove pontos" — isto é, correr concede o valor
**anterior**. Aplicando a mesma regra ao truco: correr concede 1, logo o valor anterior era 1 e
o novo é 3, não 2. A própria estrutura de F-01 refuta o "2".

## Por que isto importa

É exatamente o modo de falha que o enunciado avisa: regra de jogo errada invalida o resto. Com
`truco = 2`, a mão de onze (3 pontos, R-10) valeria mais que um truco aceito, e a escada
`1→2→6` teria um salto de 4 pontos no meio. O jogo rodaria e estaria errado.

## Lição

Fonte única, mesmo oficial, não basta — mas **a contradição interna é mais barata de achar que
a externa**: não precisei de segunda fonte para desconfiar, só de ler o documento inteiro antes
de extrair o número. Ler para extrair um campo é como se perde isso.
