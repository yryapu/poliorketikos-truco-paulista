# D-10 · Stack do servidor

Rust era dado. O que eu escolhi, e contra o quê.

## axum 0.8 — framework HTTP/WS
**Por quê:** WebSocket é de primeira classe (`axum::extract::ws`), não extensão; o extractor
`WebSocketUpgrade` entrega a conexão já atualizada e o mesmo handler enxerga os extractors de
cookie — que é exatamente o que preciso para autenticar o WS pela sessão HTTP sem inventar um
segundo mecanismo de token. Fica sobre `tower`, então middleware de trace e limite de corpo é
camada existente, não minha.
**Alternativa descartada:** `actix-web`. Descartada porque o WS dele é modelado em atores
(`actix::Actor`), um segundo modelo de concorrência ao lado do `tokio` que eu já uso para o
estado das mesas — duas disciplinas de concorrência num servidor deste tamanho é camada que não
compra capacidade.
**Também descartada:** `warp`. Composição por tipos de filtro produz erros de tipo que custam
mais a ler do que o framework economiza.

## tokio — runtime
Sem alternativa real: `axum`, `sqlx` e `reqwest` todos assumem tokio.

## sqlx 0.8 + SQLite — persistência
**Por quê:** jogador, sessão, ledger de moedas, partida e webhook são relacionais e precisam de
transação (a aposta debita dois saldos e cria uma partida — ou nada). SQLite põe isso num
arquivo, sem segundo container.
**Decisão dentro da decisão:** uso a API `sqlx::query()` **em runtime**, não as macros
`query!`. As macros checam SQL em tempo de compilação, o que é melhor — mas exigem um banco
alcançável (ou `sqlx prepare` versionado) *durante o build*, e isso contamina o `Dockerfile`
com um passo de banco. Troco checagem de compilação por um build hermético, e cubro o SQL com
testes de integração em vez disso.
**Alternativa descartada:** PostgreSQL. Correto para concorrência de escrita real, mas v1 roda
num processo; seria um segundo container e um segundo ponto de falha para zero capacidade nova.
**Alternativa descartada:** `rusqlite`. Síncrono — forçaria `spawn_blocking` em todo handler
async, ou um mutex global que serializaria o servidor.

## argon2 — hash de senha
**Por quê:** vencedor do Password Hashing Competition, é a recomendação corrente, e a crate
`argon2` do RustCrypto gera e verifica no formato PHC (`$argon2id$v=19$...`), então o parâmetro
fica gravado no próprio hash e pode ser endurecido depois sem migração.
**Alternativa descartada:** `bcrypt`. Funciona, mas tem o teto de 72 bytes de senha e não tem
custo de memória — argon2id resiste a GPU de um jeito que bcrypt não.

## Sessão: token opaco em cookie HttpOnly — não JWT
**Por quê:** 32 bytes de `OsRng` em base64url, guardados na tabela `sessao` com expiração,
entregues em cookie `HttpOnly; SameSite=Strict; Path=/`. Revogação é um `DELETE`. O cookie sobe
automaticamente no handshake do WebSocket por ser mesma origem, o que resolve autenticação de
WS sem protocolo extra.
**Alternativa descartada:** JWT. A vantagem do JWT é não consultar o banco — mas eu consulto o
banco de todo jogo de todo modo (saldo, estado), então a vantagem é zero, e o custo é real:
logout de verdade exige lista de revogação, que é a tabela de sessão de volta, com mais passos.
**Isolamento entre jogadores** (requisito do enunciado) sai disto: toda rota autenticada deriva
o `jogador_id` **do cookie**, nunca de um campo do pedido. Não existe rota que aceite
`jogador_id` do cliente.

## reqwest + HMAC-SHA256 — webhooks
**Por quê:** `reqwest` é o cliente HTTP do mesmo ecossistema. Cada registro de webhook ganha um
segredo; o POST vai assinado em `X-Truco-Signature: sha256=<hex>` sobre o corpo exato, para o
integrador poder verificar que o evento veio daqui e não de quem descobriu a URL.
**Alternativa descartada:** fila durável (Redis, tabela de outbox com worker). É o certo para
entrega garantida, e é exatamente o que v1 **não** tem — ver `riscos_conhecidos`. Entrego
`tokio::spawn` com timeout de 5s e sem retentativa, e declaro isso em vez de simular
confiabilidade que não existe.

## Front: HTML/CSS/JS sem build — não WASM
Ver `D-11-wasm.md`.
