# D-12 · Playwright para testar o front

**Escolho Playwright.** O enunciado deixa a escolha aberta e pede o porquê.

**A razão decisiva é o número de jogadores.** Uma partida de truco precisa de **2 ou 4 jogadores
simultâneos**, cada um com sessão própria e cookie próprio, cada um com seu WebSocket aberto, e
o teste tem de ver que o jogador A **não** recebe a mão do jogador B. Playwright faz isso com
`browser.newContext()`: N contextos isolados — cookie jar separado, storage separado — no mesmo
processo de browser. É a primitiva exata do requisito "dados de cada jogador isolados dos
outros", e me deixa provar o isolamento em vez de afirmá-lo.

Segundo motivo: ele dirige um **navegador real**, então o caminho que eu testo é o caminho que
existe — upgrade de WebSocket com cookie `SameSite=Strict`, handshake, mensagem com caractere
fora do BMP (`🂡` é par surrogate em UTF-16). Um simulador de DOM tipo jsdom não tem WebSocket
real nem política de cookie real; passaria verde sem exercitar nada disso.

Terceiro: espera. As asserções de Playwright re-tentam até um prazo (`expect(...).toHaveText`),
o que casa com UI movida por socket, onde o estado chega quando chega. Teste de jogo em rede
sem auto-retry é teste intermitente.

Quarto, circunstancial mas real: imagem Docker oficial com browsers já instalados. O **`node`
deste host está quebrado**, então rodar o teste de front em container não é preferência, é a
única via — e Playwright é o que tem essa via pronta.

**Alternativa descartada: Selenium/WebDriver.** Isolamento de sessão exige múltiplos drivers ou
perfis; espera é `WebDriverWait` manual, historicamente a maior fonte de teste intermitente.
**Alternativa descartada: Cypress.** Boa ergonomia, mas roda o teste *dentro* da página e
trabalha com um browser/aba por execução; orquestrar 4 jogadores simultâneos vai contra o
desenho dele. É o ponto exato onde meu caso aperta.
**Alternativa descartada: só testar o servidor** (integração HTTP/WS em `cargo test`) e declarar
o front não testado. Rejeitada porque o enunciado pede front testado, e porque o bug que eu mais
temo — carta Unicode quebrando entre JSON, WebSocket e `textContent` — só aparece no navegador.
