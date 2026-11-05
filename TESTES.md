# Plano de Verificação, Validação e Testes — Gestão de Negócios (Capítulo 8)

## 1. Estratégia de Qualidade e Pirâmide de Testes

A verificação e validação do MVP concentram-se em garantir que a fatia vertical (**Registrar venda à vista com atualização automática de estoque**) cumpra todas as regras de negócio (`RN-01` a `RN-06`) e critérios de aceitação (`CA-01.1` a `CA-03.2`) antes da demonstração do Encontro 9.

1. **Testes Unitários (Base da Pirâmide — JUnit 5 + Mockito):** Testam isoladamente as entidades (`Produto`, `Venda`, `ItemVenda`) e os serviços (`VendaService`, `EstoqueService`, `ProdutoService`) sem subir o banco de dados.
2. **Testes de Integração (Camada Intermediária — `@SpringBootTest` / `@DataJpaTest`):** Validam a atomicidade transacional (`RN-04` e `RNF-03`) e a persistência correta das entidades no PostgreSQL/H2.
3. **Inspeção e Validação Funcional (Topo da Pirâmide — Protótipo e Fluxo Web):** Conferência manual guiada pelos cenários do Caso de Uso `UC-01` junto ao usuário/equipe.

## 2. Casos de Teste Unitários e de Integração (Mapeados para Requisitos)

| ID do Teste | Alvo | Requisito / Regra | Cenário (Dado / Quando / Então) | Resultado Esperado |
|---|---|---|---|---|
| **CT-01** | `ProdutoServiceTest` | `HU-01` / `CA-01.1` | **Dado** produto com nome válido, preço `R$ 25,00` e estoque `10`, **Quando** cadastrar, **Então** persiste com sucesso. | Produto salvo com ID gerado e `ativo = true`. |
| **CT-02** | `ProdutoServiceTest` | `HU-01` / `CA-01.2` | **Dado** produto com preço `0.00` ou `-10.00`, **Quando** tentar cadastrar, **Então** rejeita operação. | Lança `IllegalArgumentException` e não chama `repository.save()`. |
| **CT-03** | `VendaDomainTest` | `RN-01` / `RN-02` | **Dado** carrinho com 2 un. de `R$ 15,00` e 1 un. de `R$ 20,00`, **Quando** calcular total, **Então** soma subtotais. | Subtotais `R$ 30,00` e `R$ 20,00`; Total = `R$ 50,00`. |
| **CT-04** | `VendaServiceTest` | `HU-02` / `CA-02.1` / `RN-04` | **Dado** produto ativo com estoque `10` e venda de `3` unidades, **Quando** confirmar venda, **Então** grava venda e baixa estoque. | Status = `CONFIRMADA` e estoque atualizado para `7`. |
| **CT-05** | `VendaServiceTest` | `HU-02` / `CA-02.2` / `RN-03` | **Dado** produto com estoque `2` e tentativa de venda de `5` unidades, **Quando** confirmar, **Então** recusa venda. | Lança `EstoqueInsuficienteException`; estoque permanece `2`. |
| **CT-06** | `VendaServiceTest` | `HU-02` / `CA-02.3` | **Dado** uma venda sem itens no carrinho, **Quando** tentar confirmar, **Então** impede confirmação. | Lança `CarrinhoVazioException`; nenhuma gravação ocorre. |
| **CT-07** | `VendaServiceTest` | `RN-06` | **Dado** um produto marcado como `ativo = false`, **Quando** adicionar à venda, **Então** recusa inclusão. | Lança `ProdutoInativoException`. |
| **CT-08** | `VendaServiceTest` | `RN-05` / `HU-11` | **Dado** uma venda `CONFIRMADA`, **Quando** solicitar cancelamento, **Então** altera status sem apagar registro. | Status muda para `CANCELADA`, estoque é estornado e registro permanece no banco. |
| **CT-09** | `VendaIntegrationTest` | `RNF-03` / `RN-04` | **Dado** uma venda com 2 itens onde o 2º item não tem estoque, **Quando** confirmar, **Então** faz rollback completo. | O estoque do 1º item não é descontado e a venda não é gravada. |

## 3. Verificação Estática e Checklist de Qualidade (Pré-Encontro 9)

Antes de abrir um Pull Request para a branch `main`, o autor e o revisor devem verificar:

- [ ] **Coesão e Critério de Parnas:** Nenhuma regra de cálculo de total ou validação de estoque foi colocada dentro de templates HTML/Thymeleaf ou classes `Controller` (`RNF-05`, `RNF-07`).
- [ ] **Segurança:** Nenhum segredo, senha de banco ou credencial foi versionado no Git (`R5`, `RNF-04`).
- [ ] **Testes Verdes:** Todos os testes unitários (`CT-01` a `CT-08`) passam via `mvn test`.
- [ ] **Rastreabilidade:** O Pull Request referencia a Issue correspondente (`#01`, `#02`, `#03`) e usa *Conventional Commits*.
