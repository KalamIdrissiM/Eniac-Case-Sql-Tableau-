# Eniac-Case-Sql-Tableau-
## 3.1 What categories of tech products does Magist have?
select distinct product_category_name
from products;
select distinct product_category_name, count(distinct product_id) 
from products
where product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia')
group by product_category_name;

select count(distinct product_id) as number_of_products, product_category_name as Product_category
from products
where product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia')
group by Product_category;

## How many products of these tech categories have been sold (within the time window of the database snapshot)? What percentage does that represent from the overall number of products sold?


select p.product_category_name as Product_category, count(distinct p.product_id) as numberofproducts_in_category, sum(ot.order_item_id) as Products_sold  -- ordr_item_id calculate number of items within order_id as well
from order_items as ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia')
group by Product_category;


## How many products sold overall in all categories
select p.product_category_name as Product_category, count(distinct p.product_id) as numberofproducts_in_category, count(distinct ot.order_id) as Products_sold  
from order_items as ot
left join products as p on ot.product_id = p.product_id
group by Product_category;

select sum(ot.order_item_id) as sold_products, p.product_category_name as Product_category
from order_items as ot
left join products as p on ot.product_id = p.product_id
group by Product_category;

select sum(order_item_id) as total_sold_products
from order_items;
select sum(ot.order_item_id) as total_sold_products
from order_items ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia');

## What percentage does that represent from the overall number of products sold?
-- total sold products = 134936
-- total sold tech products = 20309
SELECT(20309/134936*100); -- --> 15.0508%
-- perecentage from oevrall products sold can not be calculated.

## What’s the average price of the products being sold? of all products
select avg(price)
from order_items;

## of tech products?
select avg(price)
from order_items as ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia');

## what is popular? the products being sold more , meaning with higher count of order_item_id per order_id. expensive tech product not much popular if taken price individually, popular with avg and max price in each category.
select sum(ot.order_item_id) as sold_products, p.product_category_name as Product_category, ot.price as Price,
case
when sum(ot.order_item_id) > 100 then 'popular'
when sum(ot.order_item_id) <= 100 then 'not much popular'
else 'not popular'
end as popularity
from order_items as ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia')
group by Product_category, Price
order by Price desc;


select sum(ot.order_item_id) as sold_products, p.product_category_name as Product_category, avg(ot.price) as Avg_Price,
case
when sum(ot.order_item_id) > 100 then 'popular'
when sum(ot.order_item_id) <= 100 then 'not much popular'
else 'not popular'
end as popularity
from order_items as ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia')
group by Product_category
order by Avg_Price desc;


select sum(ot.order_item_id) as sold_products, p.product_category_name as Product_category, max(ot.price) as Max_Price,
case
when sum(ot.order_item_id) > 100 then 'popular'
when sum(ot.order_item_id) <= 100 then 'not much popular'
else 'not popular'
end as popularity
from order_items as ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia')
group by Product_category
order by Max_Price desc;

CREATE TEMPORARY TABLE Tech_products_order AS
SELECT order_id, order_item_id, product_id, seller_id
from order_items;

select * from Tech_products_order;


## Create a temporary table with data subset
CREATE TEMPORARY TABLE Tech_products AS
SELECT product_category_name, product_id
from products
where product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia');

select product_category_name, count(distinct product_id) as total_products
from Tech_products
group by product_category_name;

## 3.2 How many months of data are included in the magist database?
select month(order_purchase_timestamp) as order_month,
year (order_purchase_timestamp) as Order_Year
from orders
group by Order_Year, Order_month
order by Order_Year;

-- SELECT(476/3095*100); -- --> 15.3796%

select count(distinct month(order_purchase_timestamp)) as order_months,
year (order_purchase_timestamp) as Order_Year
from orders
group by Order_Year
order by Order_Year;

-- MIN(order_pruchase_timestamp)
-- MAX(order_purchase_timestamp)

## How many sellers are there? How many Tech sellers are there? What percentage of overall sellers are Tech sellers?
select count(distinct seller_id)
from order_items;

select count(distinct ot.seller_id)
from order_items as ot
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia');

-- total sellers = 3095
-- tech sellers = 511

select(511/3095*100); -- --->16.5105%

## What is the total amount earned by all sellers? What is the total amount earned by all Tech sellers?
-- total amount earned by all sellers = 16008872
-- total amount earned by tech sellers = 2937658
select truncate(sum(payment_value), 0) as total_earning
from order_payments;
select truncate(sum(payment_value), 0) as total_earning
from order_payments as op
left join order_items as ot on op.order_id = ot.order_id
left join products as p on ot.product_id = p.product_id
where p.product_category_name IN ('audio','consoles_games','eletronicos','informatica_acessorios','pc_gamer','pcs','sinalizacao_e_seguranca','telefonia');

## Can you work out the average monthly income of all sellers? Can you work out the average monthly income of Tech sellers?

-- total order months = 25(3 in 2016, 12 in 2017, 10 in 2018)
-- avg income = total income /sellers/months
-- average monthly income of all sellers = total amount earned by all sellers(16008872)/ number of sellers/ number of months 25 -- ---->206.89979968
-- average monthly income of tech sellers = total amount earned by tech sellers (2937658)/ number of tech sellers/ number of months 25 --  ----> 229.95365949
select (2937658/511/25);

select month(shipping_limit_date) as order_month, seller_id, avg(price)
from order_items
group by seller_id, order_month;

select count(distinct seller_id)
from order_items;

## 3.3 What’s the average time between the order being placed and the product being delivered?
select order_purchase_timestamp, order_delivered_customer_date
from orders;

select 
timestampdiff('order_purchase_timestamp', 'order_delivered_customer_date') as Avg_delivery_time
from orders;

select distinct order_id as order_number, 
date(order_purchase_timestamp) as order_date,
date(order_delivered_customer_date)as delivery_date,
datediff(order_delivered_customer_date, order_purchase_timestamp) Avg_delivery_time_days
from orders;

select
avg(datediff(order_delivered_customer_date, order_purchase_timestamp)) Avg_delivery_time_days
from orders;

--avg delivery time for all orders = 12.5035 days

## How many orders are delivered on time vs orders delivered with a delay?
select count(distinct order_id) as order_number,
case
when datediff(order_delivered_customer_date, order_purchase_timestamp) >20 Then 'super late delivery'
when datediff(order_delivered_customer_date, order_purchase_timestamp) >10 and datediff(order_delivered_customer_date, order_purchase_timestamp) <=20 Then 'standard time delivery'
when datediff(order_delivered_customer_date, order_purchase_timestamp) <=10 Then 'fast delivery'
else 'not delivered'
end as Delivery_speed
from orders
group by Delivery_speed;

select distinct order_id as order_number, 
datediff(order_delivered_customer_date, order_purchase_timestamp) as Avg_delivery_time_days,
order_status,
case
when datediff(order_delivered_customer_date, order_purchase_timestamp) >12.5 Then 'late delivery'
when datediff(order_delivered_customer_date, order_purchase_timestamp) <=12.5 Then 'standard time delivery'
when datediff(order_delivered_customer_date, order_purchase_timestamp) < 10 Then 'fast delivery'
else 'just delivered'
end as Delivery_speed
from orders
where order_status='delivered';


 ## Is there any pattern for delayed orders, e.g. big products being delayed more often?
-- order_status, count(distinct order_status) as number_of_orders, 
select distinct product_id, product_length_cm*product_height_cm*product_width_cm as product_size,
(case
when product_weight_g > 500 then 'heavy product'
else 'light product'
end) as product_type,
(case 
when product_length_cm*product_height_cm*product_width_cm > 500 then 'big_product'
when product_length_cm*product_height_cm*product_width_cm < 500 and product_length_cm*product_height_cm*product_width_cm >= 200 then 'medium size product'
else 'small product'
end) as product_size_type
from products
group by product_id
order by product_size_type desc;

select count(distinct product_id) as products_in_p,
case
when product_weight_g > 500 then 'big product'
else 'small product'
end as product_type
from products
group by product_type;

select distinct p.product_category_name, count(o.order_status) as number_of_orders, product_length_cm*product_height_cm*product_width_cm as product_size,
(case
when product_weight_g >= 300 then 'big product'
else 'light product'
end) as product_description,
(case
when datediff(order_delivered_customer_date, order_purchase_timestamp) >20 Then 'super late delivery'
when datediff(order_delivered_customer_date, order_purchase_timestamp) >10 and datediff(order_delivered_customer_date, order_purchase_timestamp) <=20 Then 'standard time delivery'
when datediff(order_delivered_customer_date, order_purchase_timestamp) <=10 Then 'fast delivery'
else 'not delivered'
end) as Delivery_speed
from products as p
left join order_items as oi on p.product_id = oi.product_id
left join orders as o on oi.order_id = o.order_id
group by o.order_status, product_description, Delivery_speed, product_category_name, product_size
order by product_description, Delivery_speed;

