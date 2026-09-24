# Requisitos

## Glossário do domínio

| Termo | Definição | Fonte |
|---|---|---|
| Cliente | Pessoa ou organização que compra do negócio | N4 [VALIDAR] |
| Produto | Item comercializado pelo negócio | N2/N3 [VALIDAR] |
| Estoque | Quantidade disponível de cada produto | N2/N3 [VALIDAR] |
| Venda | Operação comercial composta por um ou mais itens | N2 [VALIDAR] |
| Item de venda | Relação entre uma venda, um produto e sua quantidade/preço | N2 [VALIDAR] |
| Usuário | Pessoa autenticada que opera o sistema | N6 [VALIDAR] |
| Categoria | Classificação utilizada para organizar produtos | N3 [VALIDAR] |

## Backlog ordenado

| Ordem | Item | Origem | Classificação | Risco | Depende de |
|---:|---|---|---|---|---|
| 1 | HU-01 Cadastrar produto | N3 | Must | Médio | — |
| 2 | HU-02 Registrar venda à vista | N2 | Must | Alto | HU-01 |
| 3 | HU-03 Consultar estoque atualizado | N2/N3 | Must | Alto | HU-01, HU-02 |
| 4 | HU-04 Consultar histórico de vendas | N4 | Should | Médio | HU-02 |
| 5 | HU-05 Cadastrar cliente | N4 | Should | Médio | — |
| 6 | HU-06 Registrar conta a receber | N5 | Should | Alto | HU-02 |
| 7 | HU-07 Registrar despesa | N5 | Should | Médio | — |
| 8 | HU-08 Dashboard operacional | N1 | Should | Médio | HU-03, HU-04 |
| 9 | HU-09 Gerenciar usuários e perfis | N6 | Must | Médio | — |
| 10 | HU-10 Editar produto | N3 | Could | Baixo | HU-01 |
| 11 | HU-11 Cancelar venda | N2/N4 | Could | Alto | HU-02 |
| 12 | HU-12 Relatórios financeiros avançados | N5 | Could | Alto | HU-06, HU-07 |

## Histórias de usuário e critérios de aceitação

### HU-01 — Cadastrar produto

Como operador do negócio, quero cadastrar um produto com nome, preço e quantidade inicial em estoque para que ele possa ser vendido.

**Origem:** N3 [VALIDAR].

**CA-01.1 — caminho principal**

- Dado que o usuário está autenticado e informa nome, preço maior que zero e quantidade não negativa,
- quando confirmar o cadastro,
- então o produto é persistido e fica disponível para consulta.

**CA-01.2 — recusa**

- Dado que o preço é menor ou igual a zero,
- quando confirmar,
- então o sistema recusa a operação e não persiste o produto.

**CA-01.3 — efeito persistente**

- Dado que o cadastro foi concluído,
- quando consultar o estoque,
- então o produto aparece com a quantidade registrada.

### HU-02 — Registrar venda à vista

Como operador do negócio, quero registrar uma venda selecionando produtos e quantidades para que a operação fique armazenada e o estoque seja atualizado.

**Origem:** N2 [VALIDAR].

**CA-02.1 — caminho principal**

- Dado que o carrinho possui pelo menos um produto ativo e o estoque cobre todas as quantidades,
- quando o usuário confirmar a venda,
- então a venda e seus itens são persistidos e o estoque de cada produto é decrementado pela quantidade vendida.

**CA-02.2 — recusa por estoque**

- Dado que um produto possui estoque menor que a quantidade solicitada,
- quando o usuário confirmar,
- então a venda é recusada e o estoque permanece inalterado.

**CA-02.3 — recusa por carrinho vazio**

- Dado que o carrinho não possui itens,
- quando o usuário tentar confirmar,
- então o sistema impede a confirmação e informa que é necessário adicionar pelo menos um item.

**CA-02.4 — efeito persistente**

- Dado que a venda foi confirmada,
- quando o histórico for consultado,
- então a venda aparece com data, total, itens e usuário responsável.

### HU-03 — Consultar estoque atualizado

Como operador do negócio, quero consultar a disponibilidade dos produtos para evitar vender itens indisponíveis.

**Origem:** N2/N3 [VALIDAR].

**CA-03.1 — caminho principal**

- Dado que existem produtos cadastrados,
- quando abrir a consulta de estoque,
- então o sistema apresenta produto, preço e quantidade atual.

**CA-03.2 — consistência**

- Dado que uma venda foi confirmada,
- quando consultar o produto vendido,
- então a quantidade apresentada corresponde ao estoque após a venda.

## Caso de uso principal

### UC-01 — Registrar venda

**Ator principal:** Operador de vendas.

**Pré-condições:** usuário autenticado; produto cadastrado e ativo.

**Fluxo principal:**

1. Operador abre nova venda.
2. Pesquisa produto.
3. Seleciona produto.
4. Informa quantidade.
5. Sistema valida estoque.
6. Sistema adiciona item ao carrinho.
7. Operador confirma a venda.
8. Sistema calcula o total.
9. Sistema grava venda e itens.
10. Sistema atualiza estoque.
11. Sistema apresenta confirmação.

**Fluxos alternativos:**

- 4a. Quantidade inválida → informar erro e manter o carrinho.
- 5a. Estoque insuficiente → impedir confirmação e manter estoque.
- 7a. Carrinho vazio → impedir confirmação.
- 7b. Operador cancela antes da confirmação → descartar o rascunho sem alterar estoque.

## Requisitos não funcionais

| ID | Característica | Requisito verificável |
|---|---|---|
| RNF-01 | Eficiência de desempenho | Consulta de produtos deve responder em até 3 s com 200 produtos cadastrados em ambiente de teste definido pela equipe. |
| RNF-02 | Capacidade de interação | Um usuário que nunca utilizou o sistema deve conseguir concluir a venda principal sem ajuda em um cenário de teste preparado pela equipe. |
| RNF-03 | Confiabilidade | Uma confirmação de venda deve ser atômica: não pode persistir a venda sem os itens nem atualizar apenas parte dos estoques. |
| RNF-04 | Segurança | Senhas não devem ser armazenadas em texto puro e endpoints protegidos exigem autenticação. |
| RNF-05 | Manutenibilidade | Regras de venda e estoque devem ficar centralizadas em serviços testáveis, evitando duplicação da mesma regra em múltiplas telas/controllers. |
| RNF-06 | Compatibilidade | A aplicação deve funcionar nos dois navegadores desktop definidos no ambiente da disciplina, documentados no README. |
| RNF-07 | Flexibilidade | Regras de cálculo do total devem poder ser alteradas em um ponto identificado sem reescrever as telas de venda. |
| RNF-08 | Segurança de operação | O sistema deve impedir estoque negativo causado por uma venda validada pelo próprio sistema. |
| RNF-09 | Adequação funcional | Toda funcionalidade entregue precisa apontar para uma necessidade e possuir critério de aceitação verificável. |

## Restrições

- RE-01: aplicação web executável em ambiente acadêmico/local definido pela equipe.
- RE-02: dados do projeto devem ser fictícios ou autorizados para uso acadêmico.

## Regras de negócio

- RN-01: total da venda = soma dos subtotais dos itens.
- RN-02: subtotal do item = quantidade × preço praticado no momento da venda.
- RN-03: venda só pode ser confirmada quando todos os itens possuem estoque suficiente.
- RN-04: confirmação de venda e atualização de estoque devem fazer parte da mesma transação.
- RN-05: venda confirmada não é apagada fisicamente; eventual cancelamento deve preservar o histórico.
- RN-06: produto inativo não pode ser incluído em uma nova venda.

## Rastreabilidade

| Necessidade | Requisito | Tarefa/Issue | Código/PR |
|---|---|---|---|
| N2 | HU-02 / RN-03 / RN-04 | #02 | A preencher |
| N3 | HU-01 / HU-03 | #01 / #03 | A preencher |
| N4 | HU-04 / RN-05 | #04 | A preencher |
| N5 | HU-06 / HU-07 | #06 / #07 | A preencher |
| N6 | HU-09 / RN-06* | #09 | A preencher |

## Validações realizadas

### V1 — sessão com usuário real

**Data:** [VALIDAR]

**Perfil:** [VALIDAR]

| Achado | Efeito | Item alterado |
|---|---|---|
| [VALIDAR] | [VALIDAR] | [VALIDAR] |

## Histórico de revisão

- 2026-09-24: primeira versão preparada para o projeto Gestão de Negócios.
