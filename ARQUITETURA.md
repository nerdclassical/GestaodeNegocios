# Arquitetura e Construção — Gestão de Negócios (Capítulo 7)

## 1. Visão Arquitetural

O sistema **Gestão de Negócios** adota o estilo arquitetural em **Camadas (Layered Architecture)** monolítico modular sobre o ecossistema **Spring Boot (Java 21 LTS)**, com renderização Server-Side via **Thymeleaf** e persistência relacional em **PostgreSQL** gerenciada por **Flyway**.

### 1.1 Organização de Pacotes (Fatia Vertical do MVP)

```text
br.uft.gestaodenegocios
├── config/          # Configurações de segurança (Spring Security) e inicialização
├── controller/      # Camada de Apresentação/Web (ProdutoController, VendaController, EstoqueController)
├── dto/             # Objetos de transferência de dados e validação de entrada (Bean Validation)
├── service/         # Camada de Negócio/Aplicação (ProdutoService, VendaService, EstoqueService)
├── domain/          # Entidades JPA e regras de domínio ricas (Produto, Venda, ItemVenda, StatusVenda)
├── repository/      # Interfaces Spring Data JPA (ProdutoRepository, VendaRepository)
└── exception/       # Exceções de negócio (EstoqueInsuficienteException, CarrinhoVazioException)
```

## 2. Padrões de Projeto Aplicados (GRASP e GoF)

| Padrão | Tipo | Onde é aplicado | Problema que resolve |
|---|---|---|---|
| **Information Expert** | GRASP | `Venda` e `ItemVenda` | O cálculo do subtotal (`RN-02`) e do total da venda (`RN-01`) fica encapsulado nas próprias entidades de domínio, evitando lógica espalhada em controllers ou telas. |
| **Controller** | GRASP | `VendaController` | Recebe as requisições HTTP do Thymeleaf, valida o formato via `@Valid` e delega o fluxo de negócio para `VendaService`. |
| **Service Layer / Facade** | Arquitetural / GoF | `VendaService` | Orquestra de forma atômica (`@Transactional`) a validação de itens, baixa de estoque via `EstoqueService` e persistência da venda (`RN-04` e `RNF-03`). |
| **Repository** | DDD / Data | `ProdutoRepository`, `VendaRepository` | Abstrai consultas SQL/JPQL ao PostgreSQL, reduzindo o acoplamento entre regras de negócio e banco de dados. |
| **State (Simplificado)** | GoF | `StatusVenda` (`RASCUNHO`, `CONFIRMADA`, `CANCELADA`) | Garante que apenas vendas em `RASCUNHO` possam ser confirmadas e impede exclusão física de vendas (`RN-05`). |

## 3. Garantias de Integridade, Transação e Concorrência

Para mitigar o risco **R2 (Atualização de estoque ficar inconsistente)** e atender aos requisitos **RNF-03 (Confiabilidade)** e **RNF-08 (Segurança de operação)**:

1. **Atomicidade Transacional (`RN-04`):** O método `VendaService.confirmarVenda()` opera anotado com `@Transactional(rollbackFor = Exception.class)`. Caso qualquer item da lista falhe na validação de estoque, toda a operação sofre *rollback* automático.
2. **Controle de Concorrência Otimista:** A entidade `Produto` inclui um atributo de controle `@Version private Long versao;`, prevenindo condição de corrida (*race condition*) caso dois operadores tentem confirmar simultaneamente uma venda para a última unidade em estoque.
3. **Inativação Lógica (`RN-06`):** Tanto `Produto` (campo `ativo = true/false`) quanto `Venda` (`StatusVenda.CANCELADA`) jamais sofrem `DELETE` físico no banco, preservando a integridade referencial do histórico (`HU-04`).

## 4. Esquema Relacional Inicial (Flyway `V1__create_initial_schema.sql`)

```sql
CREATE TABLE categoria (
    id BIGSERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE produto (
    id BIGSERIAL PRIMARY KEY,
    categoria_id BIGINT REFERENCES categoria(id),
    nome VARCHAR(150) NOT NULL,
    preco NUMERIC(10, 2) NOT NULL CHECK (preco > 0),
    quantidade_estoque INTEGER NOT NULL CHECK (quantidade_estoque >= 0),
    ativo BOOLEAN NOT NULL DEFAULT TRUE,
    versao BIGINT NOT NULL DEFAULT 0
);

CREATE TABLE usuario (
    id BIGSERIAL PRIMARY KEY,
    nome VARCHAR(120) NOT NULL,
    login VARCHAR(60) NOT NULL UNIQUE,
    senha_hash VARCHAR(255) NOT NULL,
    perfil VARCHAR(30) NOT NULL
);

CREATE TABLE cliente (
    id BIGSERIAL PRIMARY KEY,
    nome VARCHAR(150) NOT NULL,
    documento VARCHAR(20),
    telefone VARCHAR(20)
);

CREATE TABLE venda (
    id BIGSERIAL PRIMARY KEY,
    usuario_id BIGINT NOT NULL REFERENCES usuario(id),
    cliente_id BIGINT REFERENCES cliente(id),
    data_hora TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(20) NOT NULL CHECK (status IN ('RASCUNHO', 'CONFIRMADA', 'CANCELADA')),
    valor_total NUMERIC(12, 2) NOT NULL CHECK (valor_total >= 0)
);

CREATE TABLE item_venda (
    id BIGSERIAL PRIMARY KEY,
    venda_id BIGINT NOT NULL REFERENCES venda(id),
    produto_id BIGINT NOT NULL REFERENCES produto(id),
    quantidade INTEGER NOT NULL CHECK (quantidade > 0),
    preco_unitario NUMERIC(10, 2) NOT NULL CHECK (preco_unitario > 0),
    subtotal NUMERIC(12, 2) NOT NULL CHECK (subtotal > 0)
);
```

## 5. Rastreabilidade Arquitetural (Decisões → Requisitos)

| Decisão / Componente | Requisitos Atendidos | Verificação |
|---|---|---|
| `CHECK (quantidade_estoque >= 0)` + `EstoqueService` | `RN-03`, `RNF-08`, `CA-02.2` | Teste unitário e constraint de banco |
| `@Transactional` em `VendaService` | `RN-04`, `RNF-03`, `CA-02.1` | Teste de integração de rollback |
| Cálculo em `Venda.calcularTotal()` | `RN-01`, `RN-02`, `RNF-05`, `RNF-07` | Teste unitário de domínio isolado |
| `BCryptPasswordEncoder` (Spring Security) | `RNF-04`, `HU-09` | Teste de configuração de segurança |
