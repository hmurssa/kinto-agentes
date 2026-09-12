# PORTAL.md — especificação do portal de locação e venda de veículos

> **Este arquivo é um esqueleto.** Ele foi gerado com a estrutura das seções, não com o
> conteúdo. Tudo marcado com `A DEFINIR` é lacuna real, não exemplo a copiar.
>
> Os agentes deste repositório tratam este documento como a única fonte de regra de
> negócio. Onde estiver `A DEFINIR`, eles devem **perguntar**, nunca deduzir. Quanto mais
> tempo uma seção ficar vazia, mais perguntas você vai responder no meio da execução.
>
> Preencha na ordem: personas, escopo, fluxos, regras, estados. As demais seções podem
> esperar.

---

## 1. Visão do produto

**Problema que o portal resolve:** A DEFINIR

**Para quem:** A DEFINIR

**Como o sucesso é medido:** A DEFINIR

---

## 2. Escopo

### Dentro do escopo

- A DEFINIR

### Fora do escopo

Esta lista é tão importante quanto a de cima. É ela que impede os agentes de alargarem a
tarefa.

- A DEFINIR

---

## 3. Personas

Os agentes só escrevem história para persona listada aqui. Persona ausente é história
bloqueada.

### Condutor

**Quem é:** A DEFINIR

**O que quer do portal:** A DEFINIR

**O que precisa comprovar para conduzir:** A DEFINIR

### Comprador

**Quem é:** A DEFINIR

**O que quer do portal:** A DEFINIR

### Atendente de estação

**Quem é:** A DEFINIR

**O que pode fazer que o condutor não pode:** A DEFINIR

### Administrador

**Quem é:** A DEFINIR

**O que pode fazer:** A DEFINIR

> Acrescente ou remova personas conforme o produto real. Não deixe persona listada sem
> preenchimento: uma persona vazia é pior que uma persona ausente, porque parece definida.

---

## 4. Vocabulário

O vocabulário do domínio está na skill `kinto-domain`. Não duplique aqui.

Se o produto usa um termo que não está lá, registre nesta seção e avise, para a skill ser
atualizada.

| Termo | Significa |
| --- | --- |
| A DEFINIR | |

---

## 5. Locação

### 5.1 Catálogo e busca

**O que o condutor vê antes de escolher:** A DEFINIR

**Filtros disponíveis:** A DEFINIR

**Ordenação padrão:** A DEFINIR

### 5.2 Disponibilidade

**Como a disponibilidade é calculada:** A DEFINIR

**Antecedência mínima para reservar:** A DEFINIR

**Antecedência máxima:** A DEFINIR

**Duração mínima e máxima da locação:** A DEFINIR

**Retirada e devolução em estações diferentes são permitidas:** A DEFINIR

**Horário de funcionamento limita retirada e devolução:** A DEFINIR

> A regra de não sobreposição já é tratada como invariante do domínio pela skill
> `kinto-domain` e pelo agente `db-postgres`. O que falta aqui são os limites acima.

### 5.3 Cadastro do condutor

Fluxo de cobertura obrigatória nos testes E2E.

**Dados solicitados:** A DEFINIR

**Documentos exigidos:** A DEFINIR

**Validações aplicadas:** A DEFINIR

**O cadastro precisa de aprovação antes de reservar:** A DEFINIR

**Idade mínima e tempo mínimo de habilitação:** A DEFINIR

**O que acontece quando a habilitação vence durante uma locação:** A DEFINIR

### 5.4 Reserva

Fluxo de cobertura obrigatória nos testes E2E.

**Passos do fluxo:** A DEFINIR

**O que pode ser alterado depois de confirmada:** A DEFINIR

**Regras de cancelamento:** A DEFINIR

**A reserva expira sem confirmação de pagamento:** A DEFINIR

**Comprovante enviado ao condutor:** A DEFINIR

### 5.5 Checkout

Fluxo de cobertura obrigatória nos testes E2E.

**Passos do fluxo:** A DEFINIR

**Meios de pagamento aceitos:** A DEFINIR

**Momento da cobrança:** A DEFINIR

**Caução ou pré-autorização:** A DEFINIR

**Política de reembolso:** A DEFINIR

**O que acontece quando o pagamento falha:** A DEFINIR

### 5.6 Retirada e devolução

**Conferência na retirada:** A DEFINIR

**Devolução fora do prazo:** A DEFINIR

**Devolução em estação diferente da combinada:** A DEFINIR

**Avaria e combustível:** A DEFINIR

### 5.7 Tarifa

**Composição do valor:** A DEFINIR

**Como a tarifa varia:** A DEFINIR

**Taxas e acréscimos:** A DEFINIR

**Moeda e arredondamento:** A DEFINIR

---

## 6. Venda

**O portal vende veículo da própria frota, de terceiros, ou ambos:** A DEFINIR

**Passos do fluxo de venda:** A DEFINIR

**O comprador precisa de cadastro equivalente ao do condutor:** A DEFINIR

**Financiamento ou pagamento à vista:** A DEFINIR

**Reserva do veículo durante a negociação:** A DEFINIR

**Um veículo pode estar simultaneamente disponível para locação e para venda:** A DEFINIR

---

## 7. Estados e transições

Os agentes não inventam nome de estado nem transição. Enquanto esta seção estiver vazia,
qualquer tarefa que dependa de ciclo de vida está bloqueada.

### Reserva

| De | Para | Quando | Quem pode |
| --- | --- | --- | --- |
| A DEFINIR | | | |

### Venda

| De | Para | Quando | Quem pode |
| --- | --- | --- | --- |
| A DEFINIR | | | |

### Veículo

| De | Para | Quando | Quem pode |
| --- | --- | --- | --- |
| A DEFINIR | | | |

---

## 8. Regras de negócio

Numere as regras e mantenha o número estável. Histórias e cenários vão citar por número.

| # | Regra | Vale para |
| --- | --- | --- |
| RN-001 | Duas reservas não podem se sobrepor para o mesmo veículo, estação e período. | Reserva |
| RN-002 | A DEFINIR | |

---

## 9. Permissões

Quem vê e quem faz o quê. Necessário para o backend decidir entre 403 e 404.

| Ação | Condutor | Comprador | Atendente | Administrador |
| --- | --- | --- | --- | --- |
| A DEFINIR | | | | |

---

## 10. Dado pessoal e LGPD

As regras técnicas de mascaramento estão na skill `lgpd-land`. O que **só pode ser
decidido aqui**:

**Finalidade de cada dado coletado:** A DEFINIR

**Base legal do tratamento:** A DEFINIR

**Prazo de retenção por tipo de dado:** A DEFINIR

**Fluxo de consentimento:** A DEFINIR

**Atendimento a direito do titular (acesso, correção, eliminação, portabilidade):** A DEFINIR

**Encarregado de dados:** A DEFINIR

**Compartilhamento com terceiros:** A DEFINIR

> Enquanto isto estiver vazio, os agentes não implementam eliminação de dado, tela de
> consentimento nem expurgo por retenção. Eles perguntam.

---

## 11. Integrações

| Sistema | Para quê | Ambiente de teste |
| --- | --- | --- |
| Pagamento | A DEFINIR | A DEFINIR |
| A DEFINIR | | |

---

## 12. Requisitos não funcionais

**Volume esperado:** A DEFINIR

**Tempo de resposta aceitável:** A DEFINIR

**Disponibilidade:** A DEFINIR

**Navegadores e dispositivos suportados:** A DEFINIR

**Nível de acessibilidade exigido:** A DEFINIR

**Idiomas:** A DEFINIR

**Fuso horário de referência:** A DEFINIR

---

## 13. Perguntas abertas

Quando um agente perguntar algo e você responder, registre a resposta na seção certa e
risque a pergunta daqui. Esta lista é o inverso do backlog: ela precisa esvaziar.

| # | Pergunta | Quem perguntou | Status |
| --- | --- | --- | --- |
| | | | |
