---
name: po-land
description: Convenção de formato de história de usuário deste projeto. Use ao escrever, refinar ou revisar épico, feature, história, critério de aceite, Definition of Ready, Definition of Done, estimativa em pontos ou dependência de backlog.
---

# po-land — formato de história

## Hierarquia

Épico → feature → história. Uma história nunca fica solta: pertence a uma feature, que pertence a um épico.

Arquivos ficam em `./specs/backlog/<epico>/<feature>.md`.

## Identificador

`KIN-<EPICO>-<NNN>`, com `<EPICO>` em três letras maiúsculas e `<NNN>` sequencial dentro do épico. Exemplo: `KIN-RES-004`.

O identificador é estável. História reescrita mantém o id. História descartada não tem o id reaproveitado.

## Formato da história

```
Como <persona>, quero <ação>, para <valor>.
```

A persona vem do `PORTAL.md`. Não crie persona nova. Se a ação não couber em nenhuma persona documentada, pergunte.

O `para <valor>` é benefício de negócio, não repetição da ação. "para poder reservar" não é valor.

## INVEST

Toda história passa pelos seis:

| Critério | Teste prático |
| --- | --- |
| Independent | Pode ir para produção sem outra história do mesmo sprint |
| Negotiable | Descreve o quê e o porquê, não o como |
| Valuable | Alguém de fora do time percebe a diferença |
| Estimable | O time sabe o bastante para dar pontos |
| Small | Cabe em um sprint com folga |
| Testable | Existe um critério objetivo de pronto |

Falhou em algum, a história não está pronta. Registre qual falhou.

## Critérios de aceite

Em Gherkin, seguindo a skill `gherkin-land`. Todo comportamento prometido pela história tem pelo menos um cenário.

## Definition of Ready

- história no formato acima, com persona do `PORTAL.md`
- critérios de aceite em Gherkin, cobrindo caminho feliz e erro
- dependências identificadas e resolvidas ou aceitas
- estimativa dada pelo time
- dado pessoal envolvido classificado conforme a skill `lgpd-land`
- nenhuma pergunta aberta bloqueando o início

## Definition of Done

- critérios de aceite verdes em teste automatizado
- cenários obrigatórios do `playwright-tester` passando, quando a história toca cadastro do condutor, reserva ou checkout
- migration com `downgrade` testado, quando houve mudança de schema
- acessibilidade verificada, quando houve mudança de interface
- log e resposta sem dado sensível exposto, conforme `lgpd-land`
- nada pendente de merge

## Estimativa

Fibonacci: 1, 2, 3, 5, 8, 13. Acima de 13, quebre a história. Estimativa é esforço relativo, não hora.

Não estime história que falhou em Estimable. Registre a pergunta que falta.

## Dependências

Declaradas por identificador, com a direção explícita: `KIN-RES-004 depende de KIN-CON-002`. Dependência circular é erro de recorte, não algo a documentar.

## Proibido

- Inventar regra de negócio ausente do `PORTAL.md`. Falta informação, pergunte.
- Escrever código de aplicação.
- Colocar solução técnica na história. Isso é trabalho do `tech-lead`.
