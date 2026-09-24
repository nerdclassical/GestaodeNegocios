# Descoberta do problema — Gestão de Negócios

> Nota de evidência: todo item marcado como [VALIDAR] é hipótese da equipe. Ele deve receber uma fonte real, data e responsável após entrevista, observação ou análise de documento.

## 1. Problema

Pequenos negócios podem ter dificuldade para acompanhar, em um mesmo fluxo de trabalho, informações de clientes, produtos, estoque, vendas e movimentações financeiras. Quando os registros ficam distribuídos entre anotações, planilhas e mensagens, aumenta a dificuldade de localizar o histórico de uma operação, conferir estoque e acompanhar o resultado do negócio. **[VALIDAR com usuário real]**

O projeto não parte de uma solução pronta; primeiro busca entender como a operação acontece atualmente, quais informações são críticas e quais problemas realmente justificam a construção do sistema.

## 2. Usuário pretendido e acesso

| Papel | Interesse | Poder de decisão | Contato/fonte |
|---|---|---|---|
| Proprietário/gestor | Acompanhar vendas, estoque e resultado | Alto | [VALIDAR] |
| Operador de vendas | Registrar vendas e consultar produtos | Médio | [VALIDAR] |
| Responsável financeiro | Acompanhar receitas e despesas | Médio | [VALIDAR] |
| Administrador do sistema | Usuários e permissões | Médio | [VALIDAR] |

## 3. Personas provisórias

### Persona P1 — Gestor do negócio

- Objetivo: acompanhar rapidamente o que foi vendido, o que está em estoque e os principais números.
- Dor provável: informações distribuídas e dificuldade de obter visão consolidada.
- Origem dos traços: hipótese da equipe; validar por entrevista/observação [VALIDAR].

### Persona P2 — Operador de vendas

- Objetivo: registrar uma venda com poucos passos e sem digitação desnecessária.
- Dor provável: consulta manual de preço e estoque, além de retrabalho no fechamento.
- Origem dos traços: hipótese da equipe; validar por entrevista/observação [VALIDAR].

## 4. Fontes consultadas

| Código | Fonte | Data | Duração | Resultado |
|---|---|---|---|---|
| F1 | Entrevista com gestor | [VALIDAR] | [VALIDAR] | Necessidades e regras |
| F2 | Observação do atendimento/venda | [VALIDAR] | [VALIDAR] | Fluxo real e exceções |
| F3 | Documento/planilha atualmente usada | [VALIDAR] | [VALIDAR] | Campos e dados atuais |

## 5. Necessidades

| Código | Necessidade | Fonte | Situação |
|---|---|---|---|
| N1 | Ter uma visão consolidada das operações do negócio | F1 [VALIDAR] | Hipótese |
| N2 | Registrar uma venda sem perder a consistência do estoque | F1/F2 [VALIDAR] | Hipótese |
| N3 | Consultar produtos, preços e disponibilidade | F2/F3 [VALIDAR] | Hipótese |
| N4 | Consultar histórico de vendas | F1/F2 [VALIDAR] | Hipótese |
| N5 | Acompanhar receitas e despesas | F1/F3 [VALIDAR] | Hipótese |
| N6 | Restringir ações conforme o perfil do usuário | F1 [VALIDAR] | Hipótese |

## 6. Conflitos e negociação a validar

| Conflito | Partes | Critério | Decisão inicial |
|---|---|---|---|
| Registrar venda rapidamente x exigir confirmação para evitar erro | Gestor / Operador | Risco de erro x tempo de operação | Confirmação final antes da persistência [VALIDAR] |
| Permitir exclusão x preservar histórico | Gestor / Financeiro | Rastreabilidade | Registros de venda não serão apagados fisicamente [VALIDAR] |

## 7. Cenário atual

1. Operador recebe o pedido.
2. Consulta produto e preço.
3. Confere disponibilidade.
4. Calcula o total.
5. Registra a operação onde estiver disponível.
6. Atualiza o controle de estoque.
7. Gestor consulta os registros posteriormente.

Etapas e exceções acima são hipóteses até a observação do processo real.

## 8. Cenário pretendido

1. Operador inicia uma venda.
2. Pesquisa e seleciona produtos.
3. Informa quantidades.
4. Sistema apresenta o total.
5. Sistema verifica estoque.
6. Usuário confirma.
7. Sistema grava a venda e os itens.
8. Sistema atualiza o estoque de maneira consistente.
9. Sistema exibe a venda registrada e permite consulta posterior.

## 9. Escopo

### Dentro do MVP

- autenticação básica;
- cadastro/consulta de produtos;
- estoque;
- registro de venda à vista;
- histórico de vendas;
- regras de integridade da venda.

### Fora do MVP

- emissão fiscal;
- integração bancária;
- gateway de pagamento;
- multiempresa;
- financeiro avançado;
- relatórios avançados.

Motivo das exclusões: não são necessárias para demonstrar a primeira fatia vertical e podem depender de integração externa ou exigir domínio que aumenta o risco do semestre.

## 10. MVP como fatia vertical

**MVP:** registrar venda à vista com atualização automática de estoque.

A fatia atravessa:

- interface;
- regras de negócio;
- persistência.

### Itenização até a review do encontro 9

Quatro iterações de uma semana: produto/estoque → carrinho/total → fechamento da venda → integração e estabilização.

## 11. Riscos iniciais

| ID | Risco | Probabilidade | Impacto | Resposta | Responsável |
|---|---|---|---|---|---|
| R1 | Requisitos mudarem após teste com usuário | Alta | Alto | Demonstrar cedo e manter backlog rastreável | Req. |
| R2 | Atualização de estoque ficar inconsistente | Média | Alto | Regra central no serviço + teste automatizado + transação | Backend |
| R3 | Inexperiência técnica da equipe | Alta | Médio | PRs pequenos, revisão e checkpoints semanais | Integração |
| R4 | Escopo crescer para fiscal/integrações | Média | Alto | Manter exclusões explícitas no backlog | Backlog |
| R5 | Credencial/segredo entrar no repositório | Baixa | Alto | .gitignore, .env.example e revisão | Integração |

## 12. Evidências que ainda precisam ser coletadas

- nome/contato do usuário real;
- data e duração das entrevistas/observações;
- confirmação ou rejeição das necessidades N1–N6;
- exceções reais do fluxo de venda;
- campos realmente usados no cadastro atual;
- regras reais de estoque, cancelamento e financeiro.
