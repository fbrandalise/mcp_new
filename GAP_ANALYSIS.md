# Gap Analysis — MCP Bling ERP
**Data:** Março 2026
**Base:** Repositório `mcp_teste` (original) × `mcp_new` (atual) × API Bling v3 completa × Casos de uso trazidos pelos clientes

---

## 1. O que está exposto hoje

### mcp_teste (ponto de partida — 9 ferramentas somente leitura)

| Ferramenta | Módulo Bling | Tipo |
|---|---|---|
| `check_stock` | Estoque | Leitura |
| `list_warehouses` | Depósitos | Leitura |
| `list_orders` | Pedidos de Venda | Leitura |
| `get_order` | Pedidos de Venda | Leitura |
| `get_nfe_status` | NF-e | Leitura |
| `list_nfe` | NF-e | Leitura |
| `search_contacts` | Contatos | Leitura |
| `get_contact` | Contatos | Leitura |
| `list_receivables` | Contas a Receber | Leitura |

### mcp_new (estado atual — 16 ferramentas, incluindo escrita)

Adicionados em relação ao `mcp_teste`:

| Ferramenta | Módulo Bling | Tipo | Observação |
|---|---|---|---|
| `list_payables` | Contas a Pagar | Leitura | Novo módulo |
| `get_product` | Produtos | Leitura | Novo módulo |
| `create_product` | Produtos | **Escrita** | Primeira operação de escrita |
| `create_kit` | Produtos (Kit) | **Escrita** | Novo módulo |
| `update_product_prices` | Produtos | **Escrita** | Atualização |
| `analyze_cash_flow` | Financeiro (calculado) | Análise | Lógica própria sobre recebíveis/pagáveis |
| `calculate_contribution_margin` | Financeiro (calculado) | Análise | Lógica própria sobre produtos |

---

## 2. O que a API Bling v3 oferece e o MCP ainda não expõe

A API Bling v3 possui **45+ entidades**. O MCP atual cobre parcialmente ~8 módulos. O quadro abaixo lista as lacunas identificadas cruzando os casos de uso dos clientes com a API disponível.

---

## 3. Gap Analysis — Classificação por Impacto × Esforço

### Legenda
- **Impacto Alto:** Solicitado por múltiplos clientes / bloqueia fluxos centrais do negócio
- **Impacto Médio:** Solicitado por clientes específicos / melhora fluxos existentes
- **Impacto Baixo:** Nicho / complementar
- **Esforço Baixo:** Wrapper direto de endpoint existente, leitura simples (< 1 dia)
- **Esforço Médio:** Múltiplas chamadas, filtros complexos ou lógica de negócio (2–5 dias)
- **Esforço Alto:** Orquestração de múltiplos endpoints, validações complexas ou emissão fiscal (> 1 semana)

---

### Quadrante 1 — Alto Impacto + Baixo Esforço (Quick Wins)

> Fazer primeiro. Retorno imediato para os clientes.

| # | Ferramenta a Criar | Módulo Bling | Caso de Uso do Cliente |
|---|---|---|---|
| 1 | `update_order_status` | Pedidos de Venda | "Quero marcar pedido como atendido/cancelado sem entrar no sistema" |
| 2 | `list_stock_movements` | Movimentações de Estoque | "Quero ver o histórico de entradas e saídas de um produto" |
| 3 | `create_receivable` | Contas a Receber | "Quero lançar um recebimento manualmente" |
| 4 | `pay_receivable` / `pay_payable` | Financeiro | "Quero dar baixa em boleto ou conta a receber" |
| 5 | `list_payment_methods` | Formas de Pagamento | "Quero saber quais formas de pagamento estão cadastradas" |
| 6 | `list_product_categories` | Categorias de Produtos | "Quero listar as categorias para classificar novos produtos" |
| 7 | `get_product_variations` | Variações de Produtos | "Meu produto tem tamanho/cor, quero consultar o estoque por variação" |
| 8 | `create_contact` | Contatos | "Quero cadastrar um cliente novo sem abrir o Bling" |
| 9 | `list_sellers` | Vendedores | "Quero saber qual vendedor fez a venda para calcular comissão" |

---

### Quadrante 2 — Alto Impacto + Esforço Médio (Projetos Principais)

> Planejar para o próximo ciclo. Alto valor, custo razoável.

| # | Ferramenta a Criar | Módulo Bling | Caso de Uso do Cliente |
|---|---|---|---|
| 10 | `create_order` | Pedidos de Venda | "Quero abrir pedido de venda para um cliente via chat" |
| 11 | `list_purchase_orders` / `get_purchase_order` | Pedidos de Compra | "Quero acompanhar o que está em pedido de compra com fornecedores" |
| 12 | `create_purchase_order` | Pedidos de Compra | "Quero gerar um pedido de compra baseado no estoque mínimo" |
| 13 | `update_stock` / `create_stock_entry` | Estoque | "Quando recebo mercadoria, quero dar entrada no estoque direto" |
| 14 | `get_shipping_tracking` | Logística / Objetos | "Quero rastrear a entrega de um pedido pelo número do objeto" |
| 15 | `list_shipping_labels` | Logística / Etiquetas | "Quero ver as etiquetas geradas para despacho" |
| 16 | `calculate_sales_commission` | Vendedores + Pedidos | "Quero calcular a comissão do vendedor no período" (lógica calculada) |
| 17 | `get_overdue_customers` | Contas a Receber + Contatos | "Me mostra os clientes com contas vencidas" (lógica cruzada) |
| 18 | `list_entry_notes` | Notas de Entrada | "Quero ver as notas de entrada de mercadoria do mês" |

---

### Quadrante 3 — Alto Impacto + Esforço Alto (Funcionalidades Complexas)

> Quebrável em fases. Emissão fiscal exige cuidado e testes extensivos.

| # | Ferramenta a Criar | Módulo Bling | Caso de Uso do Cliente |
|---|---|---|---|
| 19 | `emit_nfe` | NF-e | "Quero emitir nota fiscal de um pedido aprovado sem entrar no sistema" |
| 20 | `cancel_nfe` | NF-e | "Quero cancelar uma nota fiscal emitida por engano" |
| 21 | `create_entry_note` | Notas de Entrada | "Quero registrar a entrada de mercadoria com nota do fornecedor" |
| 22 | `generate_sales_report` | Pedidos + Produtos + Financeiro | "Quero um relatório de vendas por produto/período/vendedor" (múltiplos endpoints) |
| 23 | `reorder_suggestion` | Estoque + Pedidos Compra | "Me sugira o que comprar baseado no estoque mínimo e giro" (IA + API) |

---

### Quadrante 4 — Médio/Baixo Impacto + Esforço Variável (Backlog)

> Adicionar conforme demanda dos clientes.

| # | Ferramenta a Criar | Módulo Bling | Impacto | Esforço |
|---|---|---|---|---|
| 24 | `list_contracts` | Contratos | Médio | Baixo |
| 25 | `list_product_groups` | Grupos de Produtos | Médio | Baixo |
| 26 | `get_product_structure` (BOM) | Estruturas de Produtos | Médio | Médio |
| 27 | `list_accounting_accounts` | Contas Contábeis | Médio | Baixo |
| 28 | `emit_nfce` | NFC-e | Baixo | Alto |
| 29 | `emit_nfse` | NFS-e | Baixo | Alto |
| 30 | `list_custom_fields` | Campos Customizados | Baixo | Médio |
| 31 | `list_product_suppliers` | Fornecedores por Produto | Baixo | Baixo |
| 32 | `list_commercial_proposals` | Propostas Comerciais | Baixo | Baixo |

---

## 4. Resumo Visual — Matriz Impacto × Esforço

```
                    ESFORÇO
                Baixo     Médio     Alto
           ┌──────────┬──────────┬──────────┐
    Alto   │ 1–9 ★★★  │ 10–18 ★★ │ 19–23 ★  │
  IMPACTO  ├──────────┼──────────┼──────────┤
    Médio  │ 24,27,31 │ 26,30    │          │
           ├──────────┼──────────┼──────────┤
    Baixo  │ 25,32    │          │ 28,29    │
           └──────────┴──────────┴──────────┘

★★★ Prioridade máxima   ★★ Alta prioridade   ★ Planejar com cuidado
```

---

## 5. Casos de Uso dos Clientes — Mapeamento

Os casos de uso trazidos pelos clientes Bling foram agrupados nos seguintes temas:

| Tema | Frequência Estimada | Ferramentas que Resolve |
|---|---|---|
| Consulta e atualização de pedidos | Alta | `update_order_status`, `create_order` (gaps 1, 10) |
| Controle de estoque e movimentações | Alta | `list_stock_movements`, `update_stock`, `create_stock_entry` (gaps 2, 13) |
| Financeiro — baixa de contas | Alta | `pay_receivable`, `pay_payable`, `create_receivable` (gaps 3, 4) |
| Emissão e gestão de NFe | Alta | `emit_nfe`, `cancel_nfe` (gaps 19, 20) |
| Compras e relacionamento com fornecedores | Média | `list_purchase_orders`, `create_purchase_order` (gaps 11, 12) |
| Logística e rastreamento | Média | `get_shipping_tracking`, `list_shipping_labels` (gaps 14, 15) |
| Comissão de vendedores | Média | `list_sellers`, `calculate_sales_commission` (gaps 9, 16) |
| Inadimplência e cobrança | Média | `get_overdue_customers` (gap 17) |
| Cadastro de produtos e variações | Média | `get_product_variations`, `list_product_categories` (gaps 7, 6) |
| Relatórios gerenciais | Média | `generate_sales_report` (gap 22) |
| Sugestão de reposição | Baixa | `reorder_suggestion` (gap 23) |

---

## 6. Próximos Passos Recomendados

1. **Sprint 1 — Quick Wins** (gaps 1–9): Implementar as 9 ferramentas de alto impacto e baixo esforço. Todas são wrappers diretos da API Bling v3 com lógica mínima.

2. **Sprint 2 — Pedidos e Compras** (gaps 10–12): Adicionar criação de pedidos de venda e cobertura completa de pedidos de compra.

3. **Sprint 3 — Estoque e Logística** (gaps 13–15): Movimentações de estoque e rastreamento logístico.

4. **Sprint 4 — NFe** (gaps 19–21): Emissão e cancelamento de notas fiscais. Requer testes extensivos em ambiente de homologação.

5. **Backlog** (gaps 22–32): Priorizar conforme feedback contínuo dos clientes.

---

## 7. Referências

- Repositório base: [fbrandalise/mcp_teste](https://github.com/fbrandalise/mcp_teste) — 9 ferramentas somente leitura
- API Bling v3: [developer.bling.com.br/referencia](https://developer.bling.com.br/referencia) — 45+ entidades
- Biblioteca JS de referência: [AlexandreBellas/bling-erp-api-js](https://github.com/AlexandreBellas/bling-erp-api-js)
