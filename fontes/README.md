# Fontes

Cada fonte tem um ID (`F-nn`). O código do repositório operacional cita o ID no comentário
da regra que dela deriva. Fonte sem ID não entra.

| ID | O que é | Como foi obtida | Quando | Confiança |
|----|---------|-----------------|--------|-----------|
| F-01 | *Regras de Truco Paulista* — PDF oficial do Jogatina.com | `WebSearch` → URL do PDF em `s3.amazonaws.com/static.jogatina.com` → baixado e extraído com `pdftotext -layout`. Texto integral em `F-01-jogatina-regras-truco-paulista.txt` | 2026-10-01 | Alta para baralho/vira/manilha/empate. **Contém um erro** (ver `refutado/R-01`) |
| F-02 | *Cheat-sheet do Truco Paulista* — Jogos do Rei | `WebSearch` → `WebFetch` de `jogosdorei.com.br/blog/2026/07/02/cheatsheet-truco-paulista/` | 2026-10-01 | Alta para a escada de pontos (tabela explícita pede/aceita/corre) |
| F-03 | *Regras do Truco Paulista — guia completo* — Gama Jogos | `WebFetch` de `gamajogos.com.br/regras-do-truco.html` | 2026-10-01 | Média. Única fonte com a mão de ferro detalhada; **discorda** das outras sobre quando se pode trucar (ver `decisoes/D-02`) |
| F-04 | *Regras Oficiais do Truco* — MegaJogos | `WebFetch` de `megajogos.com.br/truco-online/regras` | 2026-10-01 | Alta para "trucar somente na sua vez" |

## URLs

- F-01 https://s3.amazonaws.com/static.jogatina.com/downloads/truco-paulista/regras-truco-paulista.pdf
- F-02 https://www.jogosdorei.com.br/blog/2026/07/02/cheatsheet-truco-paulista/
- F-03 https://gamajogos.com.br/regras-do-truco.html
- F-04 https://www.megajogos.com.br/truco-online/regras

## O que nenhuma fonte cobre

Quatro buracos. Nenhuma das quatro fontes responde, então viraram **decisão minha**, marcada
como tal em `regras/` com o prefixo `D-` em vez de `R-`. Não quero que uma escolha minha
pareça regra sourced:

1. Quem puxa a primeira rodada e como roda o embaralhador.
2. Quem puxa a rodada seguinte a um empate.
3. Truco em **1x1** — todas as quatro fontes só descrevem 2x2. F-04 chega a definir
   "Participantes: 2 duplas de jogadores".
4. Quem da dupla desafiada responde ao truco.
