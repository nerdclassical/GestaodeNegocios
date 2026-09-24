# Gestão de Negócios

Sistema web para apoiar pequenos negócios no controle de clientes, produtos, vendas, estoque e movimentações financeiras, centralizando informações e reduzindo a dependência de registros dispersos.

## Objetivo do projeto

Construir um sistema web que permita ao responsável pelo negócio acompanhar operações essenciais em um único lugar, com regras de negócio explícitas, dados persistidos e histórico rastreável.

## MVP adotado

A primeira fatia vertical é o fluxo completo de **Registrar venda à vista**:

1. Usuário entra no sistema.
2. Consulta produtos ativos.
3. Adiciona produtos e quantidades à venda.
4. Sistema calcula o total.
5. Sistema verifica disponibilidade de estoque.
6. Usuário confirma a venda.
7. Venda e seus itens são persistidos.
8. Estoque é atualizado na mesma operação.
9. Sistema exibe a confirmação da venda.

O MVP foi escolhido como fatia vertical porque passa por interface, regra de negócio e persistência, podendo ser demonstrado e verificado de ponta a ponta.

## Escopo inicial

### Dentro do produto

- Usuários e autenticação.
- Clientes.
- Categorias.
- Produtos.
- Estoque.
- Vendas.
- Histórico de vendas.
- Lançamentos financeiros simples.
- Dashboard com indicadores básicos.

### Fora do MVP

- Emissão fiscal e integração com SEFAZ.
- Gateways de pagamento.
- Integração bancária.
- Multiempresa/multifilial.
- DRE e contabilidade avançada.
- Relatórios altamente customizáveis.
- Integrações externas dependentes de credenciais.

## Tecnologias propostas

### Backend

- Java 21 LTS.
- Spring Boot.
- Spring Web.
- Spring Data JPA / Hibernate.
- Bean Validation.
- Spring Security.
- JUnit e Mockito.

### Banco de dados

- PostgreSQL.
- Flyway para versionamento das migrações.

### Frontend

- Thymeleaf para as telas do MVP.
- HTML5, CSS3 e JavaScript.
- Bootstrap para acelerar a construção da interface.

### Engenharia e colaboração

- Git.
- GitHub.
- GitHub Issues e Pull Requests.
- Conventional Commits.
- Docker Compose para executar o PostgreSQL localmente.

A escolha de Thymeleaf, em vez de uma SPA separada, reduz a complexidade de integração para um projeto acadêmico inicial e concentra o esforço nas regras de negócio e na engenharia do sistema.

## Estrutura prevista

```text
GestaodeNegocios/
├── README.md
├── CONTRIBUTING.md
├── PROCESSO.md
├── DESCOBERTA.md
├── REQUISITOS.md
├── PROJETO.md
├── CHANGELOG.md
├── .gitignore
├── diagrams/
│   ├── modelo-dominio.mmd
│   ├── modelo-dados.mmd
│   ├── sequencia-registro-venda.mmd
│   ├── estados-venda.mmd
│   └── atividades-registro-venda.mmd
└── prototipo/
    ├── index.html
    ├── dashboard.html
    ├── nova-venda.html
    ├── venda-confirmada.html
    ├── venda-sem-estoque.html
    └── README.md
```

## Estado das evidências que dependem de contato com usuário real

Os campos marcados como **[VALIDAR]** não devem ser tratados como fato observado. Eles representam hipóteses preparadas para a entrevista/observação da equipe e precisam receber fonte, data e responsável quando forem validados.

##Integrantes 
Marcos Antonio Ferreira Lima 
Dhiogo Carneiro 
Dheimerson 
