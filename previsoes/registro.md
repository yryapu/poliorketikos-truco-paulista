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

## Previsões abertas depois da transição de D-13

A partir daqui as previsões também são abertas pelo CLI `mcp__verbum__previsao`, para comparar
o Brier dele com este registro. O registro próprio continua porque **arquivo de previsão fora
dos dois repos não é prova para quem avalia** — a do CLI foi para `knowledge/predictions/` sob
o cwd, não para cá.

| ID | Claim | p | Resultado | Evidência |
|----|-------|---|-----------|-----------|
| P-006 (= CLI `8725c84e`) | `docker compose up --build` sobe o truco com healthcheck saudável na primeira tentativa | 0.60 | ✅ acertou | `docker inspect` → `healthy` em 9 s; `/api/saude` → `{"ok":true}`; container `read_only`, uid 10001, `touch /app/x` recusado. Brier do CLI: **0,16** |
| P-007 | ≥5 dos 7 testes de front passam na primeira execução | 0.45 | ✅ acertou | 6 de 7 na primeira execução; o único vermelho era asserção minha (E-07), não o jogo |
| P-010 | Rodar `verbum-pronto` antes do `resultado.json` não vai achar nada que meus 52 testes já não cubram | 0.70 | ❌ **ERREI, e feio** | Achou **dois** defeitos de correção no caminho não-feliz: embaralhamento sob 64 bits de semente, e aposta que sumia no reinício. Ver a correção do veredito em `decisoes/D-13` |
| P-008 | O corpus do reino não tem nada sobre regras de truco | 0.95 | ✅ acertou, **mas por motivo errado** | `memoria_consultar` devolveu `acertos: []` — e `paginas_varridas: 0`, `camadas: []`. Zero por falta de camada montada, não por ausência de conteúdo. Ver D-13 |
| P-009 | O corpus do reino tem algo útil sobre separação pesquisa/operacional | 0.55 | ✅ acertou | `reino_consultar` devolveu `decisions/dois-repositorios.md` e `patterns/derived-index-single-source.md` em 195 páginas varridas. Confirmou a tese que eu já havia escrito; não a mudou |
