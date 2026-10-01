# Previsões

Registradas **antes** da verificação, com p ∈ [0,1]. Fechadas depois com evidência.
Convenção: toda previsão aberta aqui antes de rodar o que a decide.

| ID | Claim | p | Resultado | Evidência |
|----|-------|---|-----------|-----------|
| P-001 | Truco aceito vale 3 pontos, e o "dois pontos" de F-01 é erro de redação | 0.93 | ✅ acertou | F-02 traz tabela `Truco \| 3 \| 1`; e a estrutura de corrida de F-01 só fecha com 3. Ver `refutado/R-01` |
| P-002 | As quatro fontes não vão cobrir truco 1x1, e eu terei de decidir | 0.80 | ✅ acertou | nenhuma das 4 menciona; F-04 define "Participantes: 2 duplas de jogadores". Virou D-03 |
| P-003 | Pelo menos duas fontes vão divergir sobre quando se pode pedir truco | 0.45 | ✅ acertou | F-03 "a qualquer momento" vs F-01/F-04 "somente na sua vez". Resolvido em D-02 |
| P-004 | O bloco Unicode *Playing Cards* é `base_do_naipe + deslocamento_do_rank`, e eu preciso pular o **Cavaleiro** (`+0xC`) além de 8/9/10 | 0.88 | ✅ acertou | 40/40 asserts contra `unicodedata.name()` em Python; `🂬 PLAYING CARD KNIGHT OF SPADES` confirmado entre J e Q. Virou o teste `recusa_cavaleiro_e_oito_nove_dez` |
| P-005 | Escrevo os ~30 testes do motor de uma vez e ≥80% passam na primeira execução; o que falhar será na tabela de empate ou na ordem de quem puxa | 0.55 | ✅ acertou, mas **subconfiante** | 31/31 na primeira execução. Ver `erros/calibracao.md` |
