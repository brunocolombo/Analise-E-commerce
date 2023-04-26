# Analise-E-commerce
Análise dos Dados da Olist, plataforma de e-commerce do Brasil.

## Contexto do Projeto
Análisar dados da Olist plataforma de e-commerce. Os dados são de quase 100mil vendas feitas na plataforma em todo o Brasil.

##Dados
Os dados utilizados nesta análise foram pegos no site Kaggle, e é composto de 6 Tabelas diferentes contendo informações de cada venda como data da compra, valor, meio de pagamento, dados do vendedor, dados do comprador e dados dos produtos vendidos.


## Ferramentas Utilizadas no Projeto
- Google Sheets
- MySQL/SQL
- Porwer BI

## Perguntas feitas
Para melhor analisar os dados, algumas perguntas foram elaboradas:

**1 - Qual o comportamento das vendas segmentando-as por Estados ou Categorias de Produtos?**

**2 - Qual o tempo médio e custo médio de entrega das mercadorias? Ela muda de acordo com o Estado do vendedor e do comprador?**

**3 - Como está a entrega das mercadorias? A maioria está dentro do prazo previsto? Ou estão atrasadas?**

**4 - Como o custo do frete muda de acordo com o o estado do vendedor e do comprador? Um frete maior afeta as vendas dos produtos?**

**5 - Existe sasonalidade nas vendas? Datas especiais, onde as vendas foram maiores?**

**6 - Quais os meios de pagamentos mais utilizados? A preferência por algum tipo de parcelamento das compras?**

## Explorando e Transformando os dados no MySQL

### Carregando os dados para o MySQL
Upload de todas as tabelas(em formato csv.) para podermos analisar e correlacionar os dados.
```sql
load data local infile "C:\\Users\\bruno\\Documents\\Brazil E-commerce\\olist_orders_dataset.csv"
into table orders_dataset
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
ignore 1 rows;
````
### Analisando os dados
Receita por Categoria
```sql
SELECT 
SUM(o.price) AS receita_total,
p.product_category_name AS categoria
FROM order_items_dataset o
JOIN products_dataset p
ON o.product_id = p.product_id
GROUP BY p.product_category_name
```
Preço médio dos produtos vendidos e do frete
```sql
SELECT
ROUND(avg(freight_value),2) AS custo_médio_frete
FROM order_items_dataset;
```
```sql
SELECT
ROUND(avg(price),2) AS preço_médio_produtos
FROM order_items_dataset;
```
Unidades Vendidas e Receita Total por dia
```sql
SELECT
od.order_purchase_date AS data_da_compra,
COUNT(oi.order_id) AS vendas_totais
SUM(price) AS receita_do_dia
FROM orders_dataset od
JOIN order_items_dataset oi
ON od.order_id = oi.order_id
GROUP BY od.order_purchase_date
ORDER BY count(oi.order_id) desc;
```
Unidades Vendidas por Categoria
```sql
SELECT 
COUNT(*) AS total_unidades_vendidas,
p.product_category_name AS categoria
FROM order_items_dataset o
JOIN products_dataset p
ON o.product_id = p.product_id
GROUP BY p.product_category_name
ORDER BY total_units_sold desc
```
Tempo(em dias) para a entrega do produto
```sql
SELECT
order_approved_at AS dia_da_compra,
order_delivered_customer_date AS dia_entrega,
DATEDIFF( order_delivered_customer_date, order_approved_at) AS tempo_entrega
FROM orders_dataset
HAVING diff is not null
ORDER BY tempo_entrega
```
Tempo de entrega previsto(em dias)
```sql
SELECT
order_approved_at AS dia_da_compra,
order_estimated_delivery_date AS dia_entrega_previsto,
DATEDIFF( order_estimated_delivery_date, order_approved_at) AS entrega_estimada
FROM orders_dataset
HAVING entrega_estimada is not null
ORDER BY order_id
```
Tipos de Pagemntos mais Utilizados
```sql
SELECT
payment_type AS Tipo_pagamento,
ROUND(COUNT(*) * 100 / SUM(COUNT(*)) OVER (),2) AS %_do_total_de_compras
FROM orders_payments
GROUP BY payment_type
```

## Dashboard Power BI
# Com o Power BI, utilizaremos de gráficos para analisar melhor os dados, e responder a todas as perguntas que fizemos aos dados de forma visual.

<img src="https://user-images.githubusercontent.com/77849519/234661413-4391eda0-2371-4270-9f3d-3b229fdf83fc.png" width="30%"> <img src="https://user-images.githubusercontent.com/77849519/234668646-d8b598ac-9d6d-4658-83a0-e0c9372ddb31.png" width="30%"> <img src="https://user-images.githubusercontent.com/77849519/234672492-a30e95f7-2e60-4164-8386-1ffbd33ffa88.png" width="30%"> 
<img src="https://user-images.githubusercontent.com/77849519/234672696-91d9688c-4488-4d70-9703-46f27b519cdf.png" width="30%"> <img src="https://user-images.githubusercontent.com/77849519/234672793-c29593dd-3863-4dae-a105-96188d1f5caa.png" width="30%">
