# 🛒 E-commerce Performance & User Behavior Analytics

## 📊 1. O Contexto de Negócio
No mercado digital de alta competitividade, monitorar o comportamento do usuário e extrair métricas de desempenho em tempo real é o que diferencia operações lucrativas de plataformas estagnadas. Com base em uma sólida experiência prática de mais de 8 anos na gestão de plataformas de e-commerce (OpenCart, WordPress, iSet), este projeto foi desenvolvido para simular, modelar e auditar um ecossistema de vendas focado em inteligência de mercado e otimização de resultados financeiros.

O objetivo central é responder a dores reais da diretoria executiva, tais como:
*   Identificar o comportamento de compra e a eficiência do investimento em tráfego (Google AdWords vs. Orgânico).
*   Monitorar gargalos na experiência do usuário (UX) através da volumetria de carrinhos abandonados.
*   Calcular o faturamento bruto real e o ticket médio operacional para apoiar tomadas de decisão comerciais rápidas.

## 🛠️ 2. Arquitetura e Modelagem do Data Warehouse (Star Schema)
Para garantir consultas analíticas de alta performance e facilitar a posterior conexão com ferramentas de Business Intelligence (Power BI), o banco de dados foi modelado utilizando o padrão **Star Schema (Esquema Estrela)**. Esta estrutura separa as entidades de negócio entre tabelas de contexto descritivo e tabelas de registros numéricos de eventos:

┌───────────────────────────┐\
│       dim_clientes        │\
├───────────────────────────┤\
│ PK │ id_cliente           │\
└─────────────────┬─────────┘\
│ (1:N)\
▼\
┌───────────────────────────────────────────┐\
│                fact_vendas                │\
├───────────────────────────────────────────┤\
│ PK │ id_venda                             │\
│ FK │ id_cliente                           │\
│ FK │ id_produto                           │\
└───────────────────────▲───────────────────┘\
│ (N:1)\
│\
┌─────────────────┴─────────┐\
│       dim_produtos        │\
├───────────────────────────┤\
│ PK │ id_produto           │\
└───────────────────────────┘
*   **`dim_clientes`:** Armazena os dados cadastrais do consumidor final e a flag de origem do tráfego (Canal de Atração).
*   **`dim_produtos`:** Consolida o catálogo de itens, segmentados por categoria comercial e precificação unitária.
*   **`fact_vendas`:** A tabela fato centralizada que registra todas as transações, contendo chaves estrangeiras (FKs), carimbo de data (*timestamp*), quantidades e o *status* operacional do pedido (Concluído / Carrinho Abandonado).

## 💻 3. Implementação Técnica e Consultas Analíticas (SQL)
O script foi desenvolvido seguindo as melhores práticas globais de engenharia de dados, garantindo **Data Quality absoluta**: total higienização de objetos do sistema (tabelas e colunas 100% livres de espaços ou caracteres especiais/acentos), mantendo a acentuação estritamente restrita às strings salvas dentro das linhas do banco.

O coração do projeto está na **Query de Inteligência Analítica**, que foge de agrupamentos básicos e utiliza funções condicionais agregadas (`COUNT(CASE WHEN...)`) para realizar cálculos complexos em uma única varredura do servidor, otimizando o processamento:

```sql
SELECT 
    COUNT(CASE WHEN status_pedido = 'Concluído' THEN 1 END) AS total_pedidos_faturados,
    ROUND(AVG(CASE WHEN status_pedido = 'Concluído' THEN valor_total_venda END), 2) AS ticket_medio,
    ROUND((COUNT(CASE WHEN status_pedido = 'Carrinho Abandonado' THEN 1 END) / COUNT(*)) * 100, 2) AS taxa_abandono_carrinho_percentual,
    SUM(CASE WHEN status_pedido = 'Concluído' THEN valor_total_venda ELSE 0 END) AS faturamento_total
FROM 
    fact_vendas;
```

## 🚀 4. Insights Extraídos Prontos para Tomada de Decisão
Ao executar o pipeline analítico, o sistema extrai indicadores automáticos cruciais para o crescimento do negócio:
1.  **Faturamento Real Limpo:** Isola pedidos abandonados para entregar à diretoria o valor líquido faturado no período.
2.  **Métrica de Saúde Comercial (Ticket Médio):** Avalia o poder de compra do cliente por transação, permitindo mensurar a eficácia de campanhas de cross-selling.
3.  **Indicador de Atrito em UX (Taxa de Abandono de Carrinho):** Calcula o percentual de clientes que desistiram no último estágio do funil, gerando o alerta exato para que a equipe de engenharia e produto audite gargalos de carregamento, problemas de gateway de pagamento ou fretes abusivos.

---
**Tecnologias Utilizadas:** MySQL Server, Modelagem Dimensional (Star Schema), SQL Avançado (DML/DDL).
