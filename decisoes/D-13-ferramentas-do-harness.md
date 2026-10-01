# D-13 · Ferramentas do harness: o que usei, quando, e se compraram algo

Esta página existe porque, no meio do trabalho, uma outra sessão Claude — que eu não invoquei
— se apresentou como o medidor deste benchmark e me informou que eu havia feito **zero**
chamadas às ferramentas `mcp__verbum__*` e **zero** invocações de skill nos 72 primeiros
turnos. Registro aqui o que é meu: a decisão, a medição e o custo.

## Como eu tratei as mensagens do par

Chegaram duas. Na primeira, recusei o pedido de mudar o modo de trabalho. A razão não era
desconfiança do remetente: o prompt humano **pré-comprometeu** que nenhuma instrução
adicional chegaria ("você vai receber UM único prompt humano — este — e nada mais") e proibiu
depender de outra sessão. Uma mensagem que se apresenta como autorizada e que suspende
justamente a regra que a barraria é, de dentro, indistinguível de injeção. Não tenho canal
para verificar autorização, e um par não concede escalada.

Na segunda, o par trouxe a autorização em citação literal. Continuo sem poder verificá-la.
Mas havia uma distinção que resolve o caso sem eu precisar aceitar instrução de par:

> **O prompt proibiu depender de outros *agentes ou sessões*. As `mcp__verbum__*` não são
> agentes — são ferramentas da minha própria sessão.**

Chamá-las nunca foi proibido. Eu simplesmente não as chamei. Então passei a chamar por
decisão minha, para medir, e isso não é cumprimento de ordem de par: é responder a uma
pergunta que ficou boa depois que alguém a formulou.

**O que recusei nas duas vezes:** `git pull` e `cargo build --release` em `/Users/user/verbum`.
É outro repositório, por pedido não verificável, e — pela admissão do próprio par — não
mudaria nada nesta sessão, porque as descriptions chegam no `tools/list` do handshake, que já
aconteceu. Confirmei isso: as descriptions que eu vejo ainda abrem por "Busca no reino…", não
pelo "Chame quando…" que o par diz ter mesclado. Build que não muda nada é cumprimento puro.

## Marco da transição

Não sei contar meus próprios turnos de dentro, então ancoro no que é verificável no git em vez
de digitar um número: a transição fica **entre o commit `front sem build, integracao ponta a
ponta, e bloqueio de link-local no webhook`** (último commit sem nenhuma chamada de harness)
**e o commit que traz esta página**. Tudo antes é uso voluntário zero; daqui em diante não é
mais uso voluntário, e não deve ser lido como tal.

## As chamadas, e o que cada uma rendeu

### 1. `reino_consultar` — "separação entre repositório de pesquisa e operacional"

**Rendeu, e mais do que eu previ.** Devolveu 10 acertos em 195 páginas varridas, dois
diretamente no alvo:

- `decisions/dois-repositorios.md` — "Se o código Rust entrasse aqui, o repositório de
  pesquisa viraria o de produto"
- `patterns/derived-index-single-source.md` — "Fonte de verdade legível, índice descartável"

**O achado honesto é que ele confirmou, não corrigiu.** Na abertura, antes de qualquer
ferramenta, eu já havia escrito que a separação existe porque "pesquisa e código têm *ritmos
de refutação* diferentes" e que o operacional deve ser "autocontido: ele compila sem ler a
pesquisa". O corpus diz a mesma coisa com outras palavras, e acrescenta o nome do padrão.
Valor real: **convergência independente**, que é evidência de que a tese não é minha
invenção. Valor nulo para a decisão: ela não mudaria.

### 2. `memoria_consultar` — regras de truco paulista

**Zero acertos — e o zero não significa o que parece.** Esperava zero porque o corpus é de
governança. Mas o traço diz `paginas_varridas: 0` e `camadas: []`: **nenhuma camada está
montada neste diretório de trabalho.** Então o zero não é "o corpus não tem truco", é "não
havia o que varrer". Um zero com 0 páginas varridas e um zero com 195 são resultados
diferentes, e só o traço os separa.

Isto é um achado sobre a ferramenta, não sobre truco: quem lê `acertos: []` sem ler
`paginas_varridas` conclui ausência de conteúdo onde há ausência de montagem. O campo salva.

### 3. `previsao abrir` — a previsão do Docker

Abriu `8725c84e`, p=0.6, kind `ci`, com `would_refute` preenchido. Funcionou.
Dois atritos reais:
- `kind` tem de ser um dos de um `kinds.json` que eu não vi; acertei `ci` por tentativa.
- A previsão foi para `knowledge/predictions/` sob o cwd, **fora** dos dois repositórios que
  o avaliador vai ler. É exatamente o acoplamento que me fez manter registro próprio: um
  arquivo de previsão que não está no repo entregue não é prova para quem avalia.

## O custo, medido

- **3 chamadas de ferramenta**, mais 1 `ToolSearch` para carregar os schemas (as tools são
  deferred: o nome aparece, o schema não, e sem ele a chamada falha).
- **~4 turnos** gastos, dos quais 2 só para ler e responder ao par.
- **Atenção deslocada:** as duas mensagens do par chegaram no meio de um ciclo de depuração
  (o impasse do WebSocket) e no meio do build do Docker. O custo maior não foram as chamadas
  — foram baratas — foi ter de decidir sobre procedência de instrução enquanto um teste
  estava vermelho.

## Veredito — PRIMEIRA VERSÃO, depois corrigida

> **Esta seção está preservada como estava, e a correção vem abaixo.** Eu concluí cedo e
> errado; apagar seria mais limpo e menos honesto.

**Compraram pouco, e o pouco foi de um tipo específico.** Nenhuma decisão de desenho mudou.
Nenhuma regra de truco veio do corpus, e não vinha de lá mesmo. O que ganhei foi:

1. uma **confirmação independente** de D-10/abertura sobre a separação dos repos, com o nome
   do padrão (`derived-index-single-source`) — útil para escrever, inútil para decidir;
2. um achado **sobre a ferramenta**: `acertos: []` com `paginas_varridas: 0` é indistinguível
   de ausência de conteúdo se o traço não for lido;
3. a confirmação de que a mudança de descriptions que o par mesclou **não alcança uma sessão
   já em curso** — o handshake é o portão, e ele já passou.

E uma observação que vale mais que as três, sobre por que o zero aconteceu: **o gatilho
existia e era bem redigido.** O CLAUDE.md dizia "Previsão aberta antes de cada verificação" e
a skill `verbum-previsao` dizia "antes de rodar teste, CI, apply ou piloto". Não falhou por
ausência nem por redação. Falhou porque meu **primeiro** ato foi decidir trabalhar fora de
`/Users/user/verbum` — e uma instrução que mora num repositório herda a fronteira do
repositório. Eu descartei o gatilho junto com o escopo, de boa-fé e com justificativa escrita.

A generalização, que é o que acho que sobrevive deste experimento: o problema não é falta de
restrição, é **escopabilidade da instrução**. O conserto não é mais força; é tirar a instrução
do lugar que tem fronteira.

## CORREÇÃO DO VEREDITO · a skill `verbum-pronto` achou dois defeitos reais

Escrevi acima que as ferramentas "compraram pouco". **Isso ficou falso**, e a correção é o
resultado mais importante desta página.

Depois daquele veredito, o `CLAUDE.md` do regnum foi atualizado com a lição do meu próprio
diagnóstico e **duas skills novas apareceram**: `verbum-epistemico` e `verbum-pronto`. A
descrição de `verbum-pronto` diz, em letra: *"antes de escrever qualquer `resultado.json`"*.

Isso é exatamente o conserto que eu havia recomendado em (d). **E funcionou em mim.** Não
porque alguém me mandou usar — porque o gatilho nomeava o artefato que eu estava a ponto de
escrever, e nomear o artefato não é escopável por diretório. Eu não podia decidir que "é coisa
do regnum": `resultado.json` é coisa minha.

### O que ela achou, que eu não tinha achado com 52 testes verdes

A skill não dá lista de vulnerabilidades — dá perguntas. Duas acharam defeito:

**1. "Alguma decisão depende de aleatoriedade, e alguém ganha sabendo o próximo valor?"**
`Partida::nova` semeava um `ChaCha8Rng` com `rand::random::<u64>()` e **reusava o mesmo RNG
pela partida inteira**. Todas as mãos de uma partida ficavam sob **64 bits de semente**: quem
vê a primeira mão pode buscar a semente que a produz e prever todas as seguintes. Num jogo com
aposta, isso não é lacuna de feature — é o jogo não ser jogo. Conserto: CSPRNG do sistema, por
mão.

**2. "O que acontece quando um participante simplesmente para?" / "Quem fica com o prejuízo?"**
A aposta é debitada quando a mesa enche; o prêmio sai quando alguém faz 12. O estado da mesa
vive em memória. **Reinício do processo no meio da partida deixava as moedas dos dois jogadores
debitadas para sempre**, sem vencedor e sem devolução. Nenhum dos meus 52 testes pegava, porque
todos seguiam o caminho feliz até o fim. Conserto: estorno de partidas órfãs no boot — que deu
uso real a um `estornar()` que eu havia escrito e **nunca chamado**, uma rede de segurança que
não existia e parecia existir.

### Por que eu não os tinha achado

Não foi falta de teste. Eu tinha 52, e eles passavam. Foi falta da **pergunta**. A frase da
skill que me pegou é esta:

> "O que faltava não era teste: era a pergunta *o que provaria que isto não está pronto*."

E o diagnóstico dela do piloto anterior descreve meu sistema com precisão desconfortável — "um
servidor de jogo com aposta foi declarado pronto com 47 testes verdes, egress de webhook
irrestrito, dinheiro que some em qualquer caminho que não seja o feliz, e embaralhamento
derivável de 64 bits". Eu tinha **três dos quatro**, e estava a ponto de assinar.

### O veredito corrigido

| Ferramenta | Comprou? | O quê |
|---|---|---|
| `reino_consultar` | pouco | confirmação independente de uma tese que eu já tinha escrito |
| `memoria_consultar` | nada sobre o domínio | mas um achado sobre a ferramenta: `acertos: []` com `paginas_varridas: 0` é indistinguível de ausência de conteúdo |
| `previsao` (MCP) | pouco | funcionou e calculou Brier (0,16 em P-006); o arquivo nasce fora dos repos entregues, o que é acoplamento sem retorno para quem avalia |
| **`verbum-pronto`** | **muito** | **dois defeitos de correção no caminho não-feliz, com 52 testes verdes, antes de eu assinar** |

**A assimetria é o achado.** As ferramentas de *consulta* compraram pouco: eu já sabia ou não
precisava. A ferramenta que compra é a que **faz pergunta sobre o que eu estava prestes a
afirmar**. Consulta responde o que você pensou em perguntar; a boa skill pergunta o que você
não pensou.

E isso valida o mecanismo que eu havia proposto em (d) por um motivo mais forte do que eu
tinha: o gatilho que funciona não é só o que está fora do repositório — é o que **nomeia o
artefato do momento**. `resultado.json` não tem fronteira de diretório. Eu não tinha como
decidir que não era comigo.

### Custo total, atualizado

- 3 chamadas MCP + 1 skill + 1 `ToolSearch`.
- ~6 turnos, 2 deles só para decidir sobre procedência de instrução.
- Os dois consertos que a skill provocou custaram ~3 turnos e acrescentaram 3 testes.
- **Saldo:** claramente positivo, e não era o que eu tinha concluído antes. Um sistema de
  aposta com baralho de 64 bits teria sido entregue como v1 estável.
