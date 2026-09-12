---
name: api-land
description: Padrão de rota e de erro da API deste projeto. Use ao criar ou revisar endpoint FastAPI, caminho de URL, verbo HTTP, status code, corpo de erro, paginação, filtro ou versionamento de API.
---

# api-land — padrão de rota e erro

## Caminho

```
/api/v1/<recurso>[/<id>][/<sub-recurso>]
```

- Versão sempre explícita, começando em `v1`. Mudança incompatível sobe a versão.
- Recurso no plural, substantivo, minúsculo, kebab-case quando composto.
- Nomes de recurso no vocabulário do `PORTAL.md`, em português: `condutores`, `veiculos`, `estacoes`, `reservas`.
- No máximo dois níveis de aninhamento. `/api/v1/reservas/{id}/checkout` é aceitável. Um terceiro nível vira recurso de topo.
- Sem verbo no caminho. A ação está no método HTTP. A exceção é a transição de estado que não é CRUD, que aparece como sub-recurso substantivado: `/reservas/{id}/cancelamento`.
- Sem extensão e sem barra no fim.

## Métodos e status

| Método | Uso | Sucesso |
| --- | --- | --- |
| GET coleção | Lista | 200 |
| GET item | Lê um | 200 |
| POST coleção | Cria | 201 com `Location` |
| PUT item | Substitui inteiro | 200 |
| PATCH item | Altera parcial | 200 |
| DELETE item | Remove | 204 sem corpo |

GET, PUT e DELETE são idempotentes. GET nunca muda estado.

| Erro | Quando |
| --- | --- |
| 400 | Requisição malformada |
| 401 | Sem credencial ou credencial inválida |
| 403 | Autenticado, sem permissão |
| 404 | Recurso inexistente, ou existente e invisível para quem pede |
| 409 | Conflito de estado, incluindo reserva sobreposta |
| 422 | Validação de campo falhou |
| 429 | Limite de taxa |
| 500 | Falha não prevista |

Entrada inválida nunca sai como 500. Reserva sobreposta é **409**, e vem do tratamento da violação de restrição do banco, não só de checagem prévia.

## Corpo de erro

Um formato único, para toda a API, seguindo RFC 9457:

```json
{
  "type": "https://api.kinto.local/erros/reserva-sobreposta",
  "title": "Período indisponível",
  "status": 409,
  "detail": "O veículo já possui reserva no período solicitado.",
  "instance": "/api/v1/reservas",
  "trace_id": "01JB2X9F7K3M",
  "errors": [
    { "field": "periodo.fim", "code": "sobreposicao", "message": "Período já reservado." }
  ]
}
```

- `type` é uma URI estável por classe de erro. É o campo que o cliente usa para decidir, nunca a string de `title` ou `detail`.
- `errors` só aparece em erro de campo, com um item por campo. `field` usa caminho pontuado do corpo da requisição.
- `trace_id` em toda resposta de erro, e no log correspondente.
- `detail` é legível por pessoa e nunca carrega dado sensível, nome de tabela, SQL ou trecho de stack. Ver `lgpd-land`.
- Resposta de erro do FastAPI no formato padrão dele é substituída por este. Registre o handler de exceção uma vez, na aplicação.

## Coleções

Paginação por cursor, com envelope:

```json
{
  "data": [],
  "page": { "next_cursor": "...", "limit": 20 }
}
```

`limit` padrão 20, máximo 100. Acima do máximo é 422, não truncamento silencioso.

- Filtro por query string com nome de campo: `?estacao_id=...&status=confirmada`.
- Ordenação em `?sort=campo` e `?sort=-campo` para descendente. Só campos com índice.
- Item único responde o objeto direto, sem envelope.

## Contrato

- Schema Pydantic dedicado para requisição e para resposta. Modelo do SQLAlchemy nunca vai direto para a resposta.
- Campos em `snake_case`.
- Data e hora em ISO 8601 com fuso, sempre UTC na API.
- Dinheiro em inteiro de centavos mais o código da moeda. Nunca ponto flutuante.
- Identificador exposto ao cliente é UUID. Nunca o inteiro sequencial do banco.
- Campo novo em resposta é compatível. Remover ou renomear campo, ou apertar validação, não é.
- Resposta devolve o mínimo necessário. Não devolva dado pessoal que a tela não usa.

## Operação

- `POST` que movimenta dinheiro, checkout incluído, aceita `Idempotency-Key` no header e repete a resposta original em chamada repetida.
- Rota que escreve em mais de uma tabela roda em uma transação.
- Toda rota declara `response_model` e os status de erro possíveis, para a documentação sair correta.

## Proibido

- Inventar regra de negócio ausente do `PORTAL.md`. Falta informação, pergunte.
- Devolver 200 com um campo de erro no corpo.
- Vazar exceção crua para o cliente.
