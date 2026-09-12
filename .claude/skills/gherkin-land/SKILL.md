---
name: gherkin-land
description: Convenção de cenários BDD deste projeto. Use ao escrever, revisar ou automatizar arquivo .feature, cenário Gherkin, passo Dado / Quando / Então, Esquema do Cenário ou tag de teste.
---

# gherkin-land — convenção de cenários

## Arquivos

Um `.feature` por história, em `./features/<epico>/<id>-<slug>.feature`. Exemplo: `./features/reserva/KIN-RES-004-reservar-veiculo.feature`.

Primeira linha sempre `# language: pt`. Palavras-chave em português: `Funcionalidade`, `Contexto`, `Cenário`, `Esquema do Cenário`, `Exemplos`, `Dado`, `Quando`, `Então`, `E`, `Mas`.

## Estrutura

```gherkin
# language: pt
@reserva @KIN-RES-004
Funcionalidade: Reservar veículo em uma estação
  Para que o condutor garanta o veículo na data que precisa

  Contexto:
    Dado que existe um condutor cadastrado e habilitado

  @smoke
  Cenário: Reserva em período disponível
    Dado que o veículo está disponível na estação no período desejado
    Quando o condutor confirma a reserva
    Então a reserva é registrada
    E o período fica indisponível para outros condutores
```

`Funcionalidade` leva uma linha de valor de negócio abaixo do título. Sem parágrafo de contexto técnico.

## Regras de escrita

- **Um comportamento por cenário. Um `Quando` por cenário.** Dois `Quando` significam dois cenários.
- `Contexto` com no máximo quatro passos, e só o que serve a todos os cenários do arquivo.
- No máximo dez cenários por arquivo. Passou disso, o recorte da história está grande.
- Passo na voz do usuário, presente do indicativo, terceira pessoa. "O condutor confirma a reserva", não "confirmar reserva" nem "eu clico em Confirmar".
- `Dado` é estado, `Quando` é a ação sob teste, `Então` é o resultado observável. Não use `Então` para preparar estado.
- Cada cenário se entende sozinho, sem ler o cenário anterior. Cenários não compartilham estado entre si.

## Linguagem de negócio, não de implementação

Proibido dentro do passo: seletor de tela, `id` de elemento, rota de API, verbo HTTP, status code, nome de tabela ou de coluna, SQL, nome de classe.

O vocabulário é o do `PORTAL.md` e da skill `kinto-domain`. Se o domínio chama de condutor, o passo não chama de usuário nem de cliente.

## Esquema do Cenário

Use quando o mesmo comportamento varia só por dado. Não use para juntar comportamentos diferentes na mesma tabela.

```gherkin
  Esquema do Cenário: Reserva recusada por período inválido
    Dado que o condutor escolhe a retirada em <retirada> e a devolução em <devolucao>
    Quando ele confirma a reserva
    Então a reserva é recusada com o motivo "<motivo>"

    Exemplos:
      | retirada   | devolucao  | motivo                          |
      | 2026-10-05 | 2026-10-04 | devolução antes da retirada     |
      | 2026-10-05 | 2026-10-05 | período sem duração             |
```

Nome de parâmetro em minúsculo, sem acento, igual ao cabeçalho da tabela.

## Tags

- `@<epico>` em todo arquivo: `@reserva`, `@condutor`, `@checkout`, `@venda`
- `@<ID>` da história: `@KIN-RES-004`
- `@smoke` nos cenários de caminho crítico
- `@obrigatorio` nos fluxos de cadastro do condutor, reserva e checkout

Sem `@wip` em arquivo comitado.

## Cobertura

Caminho feliz não basta. Todo cenário de negócio leva também os erros e as bordas que o `PORTAL.md` descreve.

Todo critério de aceite da história tem pelo menos um cenário. Critério que você não conseguiu cobrir é reportado, não omitido.

## Dado de teste

Nunca dado pessoal real em passo, tabela de `Exemplos` ou fixture. Ver a skill `lgpd-land`.

## Proibido

- Inventar regra de negócio ausente do `PORTAL.md` ou da história. Falta informação, pergunte.
- Escrever código de aplicação ou de automação junto com o `.feature`.
- Afrouxar um `Então` para o cenário passar.
