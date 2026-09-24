# Processo do projeto

## 1. Tipo de processo

O projeto adota um processo **iterativo e incremental, com pontos de controle explícitos**. A decisão não significa ausência de planejamento: o planejamento é frequente e existe rastreabilidade entre backlog, issue, branch, Pull Request e versão.

## 2. Avaliação dos cinco fatores

| Fator | Avaliação do projeto | Direção | Justificativa |
|---|---|---|---|
| Tamanho da equipe | Pequena | Ágil | A equipe consegue manter contato direto e dividir o trabalho em ramos curtos. |
| Criticidade | Baixa a moderada | Ágil | Falhas podem causar perda operacional, mas o MVP não controla domínio de risco à vida nem decisão irreversível. |
| Dinamismo | Alto | Ágil | Regras e necessidades de um negócio podem mudar conforme o usuário experimente o sistema. **[VALIDAR]** |
| Pessoal | Em formação | Plano-dirigido | A equipe precisa de mais checkpoints e documentação porque várias decisões técnicas ainda estão sendo aprendidas. |
| Cultura | Acadêmica, com autonomia | Ágil | A disciplina permite ajuste frequente desde que as decisões fiquem registradas e verificáveis. |

## 3. Escolha

A combinação dos fatores aponta para um processo iterativo e incremental com disciplina de engenharia, e não para um extremo puramente ágil ou puramente plano-dirigido.

O fator **Pessoal** é compensado por revisão de Pull Request, definição de pronto, marcos de revisão e integração frequente.

## 4. Iteração

**Duração:** 1 semana.

Cada iteração contém:

1. Planejamento e escolha de itens do backlog.
2. Implementação em ramos curtos.
3. Teste e verificação.
4. Pull Request e revisão.
5. Integração no `main`.
6. Demonstração/validação quando aplicável.
7. Atualização do backlog e dos artefatos.

## 5. Iterações até o encontro 9

| Iteração | Foco | Resultado esperado |
|---|---|---|
| I1 | Produto e estoque | Cadastro de produto persistido + consulta de estoque |
| I2 | Carrinho e regra de total | Inclusão de itens e cálculo do total |
| I3 | Fechamento da venda | Validação de estoque + persistência da venda |
| I4 | Integração do fluxo | Fluxo principal funcionando ponta a ponta + correções |

## 6. Papéis

Os papéis abaixo podem ser redistribuídos entre os integrantes.

| Papel | Responsabilidade |
|---|---|
| Responsável pelo backlog | Priorizar itens e registrar decisões de escopo |
| Responsável por requisitos | Manter DESCOBERTA.md e REQUISITOS.md |
| Responsável por domínio/dados | Manter modelos e migrações |
| Responsável por integração | Acompanhar branches, PRs e estabilidade do main |
| Revisores | Revisar PRs e difundir conhecimento do código |

## 7. Definição de pronto atual

Um item só é considerado concluído quando o comportamento esperado está verificável, o código foi integrado por PR revisado e os artefatos afetados foram atualizados.

## 8. Compensação para o fator pessoal

Como a equipe ainda está consolidando experiência, serão usados:

- branches de curta duração;
- PR pequeno;
- revisão obrigatória;
- tarefas com dependências explícitas;
- testes das regras de negócio;
- demonstrações frequentes;
- documentação curta das decisões importantes.
