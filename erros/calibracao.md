# Calibração: onde eu errei a confiança, não o fato

## P-005 — subconfiança de 0.55

Previ com `p = 0.55` que ≥80% dos ~30 testes do motor passariam de primeira. Passaram
**31/31**. Acertei o lado, mas 0.55 é quase moeda ao ar para um evento que aconteceu
folgado — e previsão mal calibrada é tão inútil quanto previsão errada, só que não parece.

**Por que eu subestimei:** ancorei na sensação de "motor de jogo de cartas tem muitos casos de
borda" em vez de olhar o que eu tinha de fato: as regras já estavam escritas como tabela em
`regras/truco-paulista.md`, com os cinco critérios de R-08 literais, e `resolver` foi escrita
como `match` sobre *slice pattern* — que força o compilador a me cobrar os comprimentos 0, 1, 2
e 3. Eu não estava escrevendo lógica, estava transcrevendo uma tabela que já tinha conferido
contra a fonte, num formato que o compilador verifica.

**A lição, que é sobre a ordem do trabalho:** pesquisar antes de codificar não só evita regra
errada — **muda a distribuição de erro do código**. Quando a especificação já é uma tabela
conferida, a implementação tende a acertar de primeira, e eu devia ter previsto mais alto por
isso. Se tivesse codificado primeiro e pesquisado depois, 0.55 teria sido generoso.

**Correção de processo:** ao prever acerto de implementação, perguntar antes "a especificação
disto já existe conferida, e em forma que o compilador cheque?". Se sim, subir `p`.
