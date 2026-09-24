# Projeto do sistema — Gestão de Negócios

## 1. Modelo de domínio

O modelo de domínio representa os conceitos do negócio, não classes específicas de framework.

| Classe | Requisito que a exige |
|---|---|
| Usuario | HU-09 |
| Cliente | HU-05 / HU-04 |
| Categoria | HU-01 / HU-10 |
| Produto | HU-01 / HU-03 |
| Venda | HU-02 / HU-04 / HU-11 |
| ItemVenda | HU-02 |
| LancamentoFinanceiro | HU-06 / HU-07 / HU-08 |

### Associações e multiplicidades

- `Categoria 1 ---- 0..* Produto`
- `Usuario 1 ---- 0..* Venda`
- `Cliente 0..1 ---- 0..* Venda`
- `Venda 1 ---- 1..* ItemVenda`
- `Produto 1 ---- 0..* ItemVenda`
- `Venda 0..1 ---- 0..* LancamentoFinanceiro` [validar regra financeira]

## 2. Modelo de dados

Tabelas principais:

- `usuario`
- `categoria`
- `produto`
- `cliente`
- `venda`
- `item_venda`
- `lancamento_financeiro`

### Decisões registradas

| Tema | Decisão | Motivo | Consequência aceita |
|---|---|---|---|
| Identidade | IDs numéricos gerados pelo banco | Simplicidade e integração com JPA | IDs são sequenciais internamente |
| Apagamento | Venda não recebe exclusão física | Preservar histórico | Exige fluxo de cancelamento separado |
| Produto | Preferir inativação a apagar produto usado em histórico | Não quebrar referências históricas | Pode haver produtos inativos na base |
| Tempo | Backend trabalha com data/hora do evento e banco registra o instante | Ordenar histórico de vendas | Conversão de exibição precisa ser definida |
| Arquivo | Fora do MVP | Evitar infraestrutura adicional | Documento de estoque/nota não será armazenado na primeira fatia |

## 3. Estado do objeto central: Venda

Estados:

- `RASCUNHO`
- `CONFIRMADA`
- `CANCELADA`

Transições principais:

- RASCUNHO → CONFIRMADA: carrinho válido + estoque suficiente + confirmação do operador.
- RASCUNHO → CANCELADA: operador abandona/cancela antes da confirmação.
- CONFIRMADA → CANCELADA: somente por regra de negócio explícita e preservando o histórico.

## 4. Responsabilidade entre partes

| Parte | Responsabilidade |
|---|---|
| Controller | Receber requisição, validar formato, delegar e montar resposta |
| VendaService | Coordenar o caso de uso de venda e regras da operação |
| EstoqueService | Verificar disponibilidade e aplicar movimentação de estoque |
| ProdutoService | Cadastro e consulta de produtos |
| Repository | Persistência e recuperação de dados |
| Entidades de domínio | Representar estado e invariantes locais |
| Interface | Exibir fluxo e mensagens sem conter regra crítica de negócio |

## 5. Acoplamento e coesão

A regra de estoque será mantida em um ponto central, para evitar que cada tela implemente uma versão diferente da mesma validação.

A regra de cálculo do total também ficará isolada do HTML, evitando duplicação entre tela e backend.

## 6. Decisões de projeto

| ID | Decisão | Motivo | Consequência aceita |
|---|---|---|---|
| DP-01 | Arquitetura em camadas | Separar interface, regra e persistência | Mais classes e interfaces para um sistema pequeno |
| DP-02 | Spring Boot no servidor | Padronizar configuração e facilitar testes | Dependência de ecossistema Spring |
| DP-03 | PostgreSQL | Persistência relacional adequada às relações do domínio | Necessidade de migrações e ambiente de banco |
| DP-04 | Thymeleaf no MVP | Reduzir integração frontend/backend durante o semestre | Interatividade de SPA não é foco inicial |
| DP-05 | Regras de venda em serviço transacional | Garantir consistência de venda + estoque | Serviço concentra responsabilidade importante |
| DP-06 | GitHub PR obrigatório | Rastrear discussão e revisão | Pequenas mudanças exigem disciplina de integração |

## 7. Critério de Parnas aplicado

A decisão com maior probabilidade de mudar é o conjunto de regras do processo de venda e, posteriormente, descontos e políticas de cancelamento.

Para reduzir o impacto de mudança, essas regras não serão espalhadas por controllers, HTML e repositories. Elas ficam concentradas em serviços de domínio/caso de uso, com testes próprios.

## 8. Conferência cruzada

| Backlog | Modelo | Dados | Protótipo | Situação |
|---|---|---|---|---|
| HU-01 Produto | Produto | produto | Tela de cadastro | Coerente |
| HU-02 Venda | Venda + ItemVenda + Produto | venda + item_venda | Nova venda + confirmação | Coerente |
| HU-03 Estoque | Produto | produto | Consulta de estoque | Coerente |
| HU-04 Histórico | Venda + ItemVenda | venda + item_venda | Histórico [próxima iteração] | Coerente |

## 9. Diagramas

As definições textuais ficam na pasta `diagrams/`.

- modelo de domínio;
- modelo de dados;
- sequência do registro da venda;
- estados da venda;
- atividades do registro de venda.

## 10. Protótipo

O fluxo navegável do MVP está em `prototipo/`, incluindo as recusas de estoque insuficiente e carrinho vazio.

## 11. Lacunas descobertas pela modelagem

- Definir política real de cancelamento com o usuário.
- Confirmar se venda pode existir sem cliente.
- Confirmar se haverá vendas parceladas no semestre.
- Confirmar como o negócio registra devolução.
- Confirmar regras de alteração de preço e histórico de preço.
