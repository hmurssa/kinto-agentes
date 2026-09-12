---
name: kinto-domain
description: Vocabulário e modelo conceitual do portal de locação e venda de veículos. Use ao nomear entidade, tabela, rota, componente, variável ou passo de teste, e sempre antes de introduzir um termo novo de negócio.
---

# kinto-domain — vocabulário do domínio

Esta skill define **como as coisas se chamam**. Ela não define regra de negócio.

Regra de negócio mora em `./specs/PORTAL.md`. Se você precisa de um prazo, um limite, um preço, uma condição ou uma política e não encontrou no `PORTAL.md`, **pergunte**. Não deduza a partir do que está escrito aqui.

## Uma palavra por conceito

O maior risco do projeto é o mesmo conceito ganhar três nomes entre a história, a tabela e a tela. A coluna da esquerda é a única forma aceita.

| Use | Nunca use |
| --- | --- |
| condutor | usuário, cliente, motorista, locatário |
| veículo | carro, automóvel, unidade, item |
| estação | filial, loja, ponto, unidade, agência |
| reserva | booking, agendamento, pedido, aluguel |
| locação | aluguel, rental |
| venda | compra, negociação |
| período | intervalo, datas, range |
| disponibilidade | estoque, vacância |
| retirada | pickup, saída, início |
| devolução | dropoff, entrega, retorno, fim |
| checkout | fechamento, pagamento, finalização |
| cancelamento | desistência, estorno |
| tarifa | preço, valor, diária |

O termo vale em todas as camadas: história, `.feature`, tabela, rota, schema, componente e nome de variável.

## Entidades

Atributos marcados com `?` existem no conceito mas ainda não têm definição no `PORTAL.md`. Antes de implementar um deles, pergunte.

**Condutor** — pessoa física que reserva e conduz o veículo. Identidade, documento de habilitação, contato. Elegibilidade para conduzir: `?`

**Veículo** — unidade física, identificada individualmente. Pertence a um modelo e está lotado em uma estação. Situação operacional: `?`

**Modelo** — categoria comercial do veículo, com as características que o condutor compara ao escolher. Um modelo tem muitos veículos.

**Estação** — local físico onde o veículo é retirado e devolvido. Endereço, horário de funcionamento: `?`

**Período** — faixa de tempo contínua, com início na retirada e fim na devolução. É uma faixa, não dois campos soltos. Sempre com fuso.

**Disponibilidade** — resposta à pergunta "este veículo pode ser reservado nesta estação neste período". Derivada das reservas existentes e da situação do veículo. Não é um cadastro.

**Reserva** — compromisso de um condutor sobre um veículo, em uma estação, por um período. É a entidade central da locação.

**Locação** — a reserva em execução, do momento da retirada até a devolução.

**Venda** — transação de transferência de propriedade de um veículo. Fluxo distinto da locação, sobre o mesmo catálogo de veículos.

**Checkout** — o ato de fechar a reserva ou a venda, com pagamento. Ver a skill `api-land` quanto a idempotência.

**Tarifa** — o que determina o valor. Composição e regras de cálculo: `?`

## Invariante que atravessa tudo

**Duas reservas não podem se sobrepor para o mesmo veículo, estação e período.** Isso não é preferência de implementação, é a regra que define o domínio.

A garantia final é do banco, por restrição de exclusão, não de checagem na aplicação. Ver o agente `db-postgres`. A API traduz a violação em 409, conforme `api-land`.

Reserva cancelada não ocupa o período. O tratamento disso faz parte da própria restrição.

## Nomes no código

O domínio é escrito em português, sem acento e sem cedilha nos identificadores. O idioma não muda de camada para camada.

| Camada | Forma | Exemplo |
| --- | --- | --- |
| Tabela | plural, `snake_case` | `reservas`, `veiculos`, `estacoes` |
| Coluna | singular, `snake_case` | `condutor_id`, `periodo`, `estacao_id` |
| Rota | plural, minúsculo | `/api/v1/reservas` |
| Campo JSON | `snake_case` | `condutor_id`, `data_retirada` |
| Classe Python | `PascalCase` | `Reserva`, `Veiculo`, `Estacao` |
| Componente React | `PascalCase` | `CardVeiculo`, `FormularioReserva` |
| Arquivo React | `PascalCase.tsx` | `FormularioReserva.tsx` |

Inglês fica reservado para termos técnicos sem tradução consagrada: `checkout`, `token`, `cursor`, `id`.

## Estados

Reserva e venda têm ciclo de vida. Os estados e as transições permitidas **não estão definidos** no `PORTAL.md`. Enquanto não estiverem:

- não invente nome de estado
- não implemente transição por dedução
- pergunte, e registre a resposta no `PORTAL.md` antes de codar

## Termo novo

Precisou de um conceito que não está nesta tabela? Duas opções, nesta ordem: achar o termo que o `PORTAL.md` já usa, ou perguntar. Criar o termo por conta própria é a terceira opção e ela não existe.
