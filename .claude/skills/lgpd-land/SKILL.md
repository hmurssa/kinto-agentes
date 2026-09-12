---
name: lgpd-land
description: Regras de dado pessoal deste projeto. Use ao escrever log, resposta de API, mensagem de erro, fixture, seed, dado de teste, ou ao tocar em CPF, CNH, endereço, telefone, e-mail, cartão ou qualquer dado de pessoa.
---

# lgpd-land — dado pessoal

Vale para código de aplicação, teste, log, seed, fixture, script de apoio e qualquer saída que uma pessoa possa ler.

## Classificação

**Dado pessoal** — identifica alguém: nome, CPF, CNH, RG, passaporte, e-mail, telefone, endereço, placa, data de nascimento, IP, identificador de dispositivo.

**Dado sensível ou financeiro** — tratamento mais restrito: número de cartão, CVV, validade, dado bancário, biometria, foto de documento, geolocalização precisa, dado de saúde.

**Segredo** — nunca aparece em lugar nenhum: senha, hash de senha, token, chave de API, cookie de sessão, `Authorization`.

## Log

Log é a fonte mais comum de vazamento. Três regras.

**Segredo nunca vai para o log.** Nem mascarado, nem em amostra, nem em modo debug. Isso inclui o header `Authorization` e o corpo do login.

**Dado pessoal vai mascarado**, quando precisar ir:

| Dado | No log |
| --- | --- |
| CPF | `***.***.**9-01` |
| CNH | `*********01` |
| E-mail | `j***@dominio.com` |
| Telefone | `(11) *****-4321` |
| Cartão | `**** **** **** 4321`, bandeira separada |
| CVV, validade | nunca |
| Nome | iniciais, ou o id interno |
| Endereço | cidade e estado, nada mais fino |
| Coordenada | duas casas decimais |

**Corpo de requisição e de resposta nunca é logado cru** em rota que trafega dado pessoal. Logue o `trace_id`, a rota, o método, o status, a duração e o id do recurso. O conteúdo, não.

Prefira sempre o identificador interno ao dado pessoal: `condutor_id=uuid` diz o mesmo que o CPF e não vaza nada.

A máscara é aplicada no filtro do logger, não na chamada. Depender de quem chama lembrar de mascarar é garantia de que um dia não vai acontecer.

## Resposta da API

- Devolva o mínimo que a tela usa. Listagem de reservas não precisa do CPF do condutor.
- Documento completo só para o próprio titular, ou para papel com permissão explícita no `PORTAL.md`. Fora disso, mascarado.
- Cartão nunca volta completo. Só bandeira e últimos quatro dígitos.
- Mensagem de erro não confirma existência de pessoa. Em recuperação de senha e login, a resposta é a mesma para e-mail existente e inexistente.
- Nada de dado pessoal em URL, nem em caminho nem em query string. URL vai para log de servidor, de proxy e de navegador. Use o corpo, ou o UUID.
- `detail` de erro não carrega o valor que falhou, quando o valor é dado pessoal. Nomeie o campo, não o conteúdo.

## Fixture, seed e dado de teste

**Nenhum dado real de pessoa, em nenhuma hipótese.** Vale para `.feature`, fixture de teste, seed, snapshot e exemplo em documentação.

- Dado sintético, gerado com semente fixa, para o teste ser determinístico.
- CPF e CNH sintéticos: válidos no algoritmo de verificação, e nunca de pessoa existente.
- E-mail em domínio reservado: `@example.com`, `@example.org`.
- Telefone em faixa reservada para ficção.
- Nada de número de cartão real. Use os números de teste do provedor de pagamento.
- **Dump de produção não é copiado para desenvolvimento, homologação ou teste.** Nem anonimizado por conta própria, sem processo definido.

Uma pessoa real reconhecível no repositório é incidente, não deslize de estilo. Pare e avise.

## Código

- Dado pessoal não vai para mensagem de exceção, pois a exceção acaba em log e em rastreador de erro.
- Ao integrar rastreador de erro ou ferramenta de observabilidade, confirme que o envio de corpo de requisição e de variável local está desligado.
- Campo que guarda dado sensível é anotado no modelo, para o filtro de log e o serializador saberem tratá-lo.
- Busca por dado pessoal nunca aparece na URL. Use `POST` em um sub-recurso de busca.

## O que não é decidido aqui

Prazo de retenção, base legal, finalidade do tratamento, fluxo de consentimento e atendimento a direito do titular vêm do `PORTAL.md`. Se não estiverem lá, **pergunte**. Não deduza prazo, não invente tela de consentimento, não implemente eliminação de dado por conta própria.

## Proibido

- Log de segredo, em qualquer nível.
- Dado pessoal completo em log, URL ou mensagem de erro.
- Dado real de pessoa em fixture, seed ou documentação.
- Desligar o mascaramento para depurar, mesmo temporariamente, mesmo local.
