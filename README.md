# Kinto Agentes

Plugin do Claude Code com uma equipe de nove agentes e cinco skills para construir um
portal de locação e venda de veículos.

A ideia central é que nenhum agente invente regra de negócio. Todos leem
[`specs/PORTAL.md`](specs/PORTAL.md) como única fonte da verdade e perguntam quando falta
informação.

---

## Instalação

### A partir do GitHub

```
/plugin marketplace add hmurssa/kinto-agentes
/plugin install kinto-agentes@kinto-agentes
```

Se a instalação pedir, rode `/reload-plugins` para ativar sem reiniciar.

### Localmente, para desenvolver

```
git clone https://github.com/hmurssa/kinto-agentes
claude --plugin-dir ./kinto-agentes
```

### Verificação

Os agentes aparecem em `/context`, na seção de agentes personalizados. As skills ficam
com o prefixo do plugin, por exemplo `/kinto-agentes:po-land`.

---

## Os nove agentes

O `orquestrador` é a porta de entrada. A intenção do desenho é falar só com ele, e deixar
que ele escolha os especialistas.

| Agente | Quando ele entra |
| --- | --- |
| `orquestrador` | Sempre. Classifica a tarefa, escolhe o especialista, valida a saída |
| `user-story-reader` | A história está num card do Trello e ainda não existe no repositório |
| `product-owner` | É preciso virar o `PORTAL.md` em épicos, features e histórias |
| `tech-lead` | A história fechou e precisa ser quebrada em subtarefas com ordem de execução |
| `bdd-gherkin` | A história foi aprovada e precisa de cenários executáveis |
| `db-postgres` | A tarefa mexe em schema, migration, seed ou disponibilidade |
| `backend-python` | A tarefa envolve endpoint, contrato de API ou regra no servidor |
| `frontend-react` | A tarefa muda algo que o usuário vê ou com que interage |
| `playwright-tester` | Há interface pronta para validar ponta a ponta, ou vai sair release |

### O que cada um faz

**`orquestrador`** classifica a tarefa entre história, bug, spike, refactor e infra,
escolhe o especialista pela descrição de cada um, passa contexto mínimo com critérios de
aceite e link do card, executa em série quando há dependência e em paralelo quando não há,
valida a saída contra os critérios antes de devolver, e reporta uma linha por especialista.
Não escreve código de aplicação.

**`user-story-reader`** lê o card do Trello pelas ferramentas MCP, incluindo checklists,
comentários e labels, e devolve a história estruturada. Transcreve o que está no card e
lista as lacunas em vez de preenchê-las.

**`product-owner`** escreve histórias no formato "Como persona, quero ação, para valor",
aplica INVEST, e entrega critérios de aceite em Gherkin, Definition of Ready, Definition
of Done, estimativa em pontos e dependências.

**`tech-lead`** faz o refinamento técnico e quebra a história em subtarefas de backend,
frontend, banco e QA, com dependências explícitas, ordem de execução e riscos. Especifica
o trabalho, não executa.

**`bdd-gherkin`** transforma histórias em arquivos `.feature`, um comportamento por
cenário, em linguagem de negócio, cobrindo erro e borda além do caminho feliz.

**`db-postgres`** cuida de modelagem, migrations e seed. Garante no banco, por restrição de
exclusão sobre faixa de tempo, que duas reservas nunca se sobreponham para o mesmo
veículo, estação e período.

**`backend-python`** implementa a API com FastAPI, SQLAlchemy, Pydantic e Alembic. É o dono
do contrato da API. Traduz violação de restrição do banco em conflito HTTP.

**`frontend-react`** implementa a interface com React 18, Vite e TypeScript, React Router e
estado com hooks. Acessibilidade e performance são requisito, não polimento. Todo valor
visual vem de [`specs/design-tokens.json`](specs/design-tokens.json).

**`playwright-tester`** escreve e roda os testes end-to-end. Três fluxos têm cobertura
obrigatória: cadastro do condutor, reserva e checkout. Reporta bug, não conserta.

---

## As cinco skills

| Skill | Para quê |
| --- | --- |
| `kinto-domain` | Vocabulário e modelo conceitual. Uma palavra por conceito |
| `po-land` | Formato de história, INVEST, Definition of Ready e Done, estimativa |
| `gherkin-land` | Convenção de cenários, estrutura do `.feature`, tags |
| `api-land` | Padrão de rota, status, corpo de erro, paginação e contrato |
| `lgpd-land` | Dado pessoal mascarado em log e resposta, nada real em fixture ou seed |

A `kinto-domain` define **como as coisas se chamam**, não o que elas fazem. Regra de
negócio mora no `PORTAL.md`.

---

## Especificação

[`specs/PORTAL.md`](specs/PORTAL.md) é a única fonte de regra de negócio.

**Ele está propositalmente vazio.** As treze seções têm a estrutura pronta e cada lacuna
está marcada com `A DEFINIR`. Isso é deliberado: os agentes tratam o arquivo como
autoritativo, então preenchê-lo com regras plausíveis faria com que invenções fossem
implementadas com a autoridade de especificação aprovada. Um documento vazio gera
perguntas, que é o comportamento correto.

Preencha na ordem: personas, escopo, fluxos, regras, estados. Com as seções de personas e
de estados vazias, qualquer tarefa que dependa de ciclo de vida de reserva fica bloqueada.

A única regra já definida é a **RN-001**, a não sobreposição de reservas.

[`specs/design-tokens.json`](specs/design-tokens.json) tem os tokens de cor, tipografia,
espaçamento, raio, sombra, foco, breakpoint e movimento. Os 29 pares de texto e fundo dos
tokens semânticos foram verificados e todos passam 4.5:1. A paleta é um ponto de partida
neutro, não a identidade de marca.

---

## Servidores MCP

O [`.mcp.json`](.mcp.json) na raiz declara dois servidores, e **instalar este plugin
habilita os dois**:

| Servidor | Pacote | Oficial |
| --- | --- | --- |
| `trello` | `@delorenj/mcp-server-trello` | não, é comunitário |
| `github` | `https://api.githubcopilot.com/mcp/` | sim |

Para usar, copie `.env.example` para `.env`, preencha, e reinicie o Claude Code. O
`.mcp.json` só é lido na inicialização. Confirme com `/mcp`.

O `user-story-reader` depende do servidor do Trello. Sem ele, o agente avisa e para em vez
de adivinhar o conteúdo do card.

---

## Credenciais

Nenhuma credencial ou token está versionado, e nenhuma deve estar.

- O `.mcp.json` referencia apenas variáveis de ambiente, na forma `${VAR}`.
- O `.env` está no `.gitignore`. Só o `.env.example`, com valores vazios, é versionado.
- A skill `lgpd-land` proíbe dado real de pessoa em fixture, seed e documentação, e exige
  mascaramento de dado pessoal em log e resposta.

Use um token do GitHub de escopo fino, restrito a este repositório.

---

## Limitações conhecidas

**O orquestrador pode não conseguir delegar.** Um sub-agente normalmente não abre outro
sub-agente no Claude Code. Se isso valer na sua versão, o orquestrador será escolhido como
porta de entrada e vai travar no passo de despachar. A alternativa é usar o papel dele na
sessão principal, como skill. Teste com uma tarefa pequena antes de apostar no desenho.

**A lista de ferramentas do orquestrador não foi validada.** Ela declara `Task`, que é o
nome antigo da ferramenta de delegação, hoje `Agent`. Declara também `mcp__trello` e
`mcp__github`, que são nomes de servidor, enquanto as ferramentas reais têm a forma
`mcp__trello__algo`. Rode `/mcp` depois de conectar e ajuste.

**Agentes locais têm precedência.** Uma definição em `.claude/agents/` do projeto ou do
usuário sobrescreve o agente de plugin com o mesmo nome. Nesta pasta isso é o esperado,
porque as definições são as mesmas.

**As skills trocam de nome quando instaladas.** Os agentes pedem a skill `kinto-domain`
pelo nome simples, que é como ela existe quando este repositório é usado direto. Instalado
como plugin, ela passa a ser `kinto-agentes:kinto-domain`. O Claude encontra a skill pela
descrição, então na prática resolve, mas o nome citado no prompt do agente não é o nome
final.

---

## Estrutura

```
.claude-plugin/
  plugin.json         manifesto do plugin
  marketplace.json    permite instalar direto do GitHub
.claude/
  agents/             os nove agentes
  skills/             as cinco skills
specs/
  PORTAL.md           especificação, a preencher
  design-tokens.json  tokens de design
.mcp.json             servidores Trello e GitHub
.env.example          credenciais a preencher
```
