# Guia de Contribuição — Gestão de Negócios

## 1. Fluxo de Trabalho (Git Flow Simplificado)

1. Toda alteração deve partir de uma **Issue** cadastrada no GitHub e vinculada a um item do backlog (`HU-01` a `HU-12`).
2. Crie uma branch curta a partir da `main`:
   - `feat/hu-01-cadastro-produto`
   - `feat/hu-02-registro-venda`
   - `docs/atualizacao-arquitetura`
   - `fix/calculo-subtotal`
3. Faça commits pequenos utilizando **Conventional Commits**:
   - `feat: implementa baixa automatica de estoque na confirmacao da venda`
   - `test: adiciona testes unitarios para recusa de estoque insuficiente`
   - `docs: adiciona especificacao de arquitetura e plano de testes`
4. Abra um **Pull Request (PR)** descrevendo o que foi feito, qual requisito foi atendido e como testar.
5. O merge na `main` exige revisão e todos os testes passando.
