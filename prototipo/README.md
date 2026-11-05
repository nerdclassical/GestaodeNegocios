# Protótipo Navegável — Gestão de Negócios (MVP)

Este diretório contém o fluxo navegável em HTML5 + Bootstrap 5 correspondente à primeira fatia vertical do sistema (**Registrar venda à vista com atualização automática de estoque**).

## Arquivos e Cenários Cobertos

1. `index.html`: Tela de autenticação básica do operador (`HU-09`).
2. `dashboard.html`: Visão consolidada e consulta de produtos ativos e estoque (`HU-01`, `HU-03`, `HU-08`).
3. `nova-venda.html`: Tela de registro de venda em estado `RASCUNHO` com cálculo de subtotais e total (`HU-02`, `RN-01`, `RN-02`).
4. `venda-confirmada.html`: Confirmação da venda (`CA-02.1`) demonstrando a atualização atômica do estoque (`RN-04`).
5. `venda-sem-estoque.html`: Fluxo alternativo de recusa por estoque insuficiente (`CA-02.2`, `RN-03`, `RNF-08`).

## Como Executar

Basta abrir o arquivo `index.html` diretamente em qualquer navegador desktop moderno.
