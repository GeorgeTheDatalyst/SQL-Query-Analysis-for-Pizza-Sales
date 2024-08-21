 # Project Report: SQL Query Analysis for Pizza Sales  
 ## 1. Introduction  
 ## 1.1 Project Overview  
This project aims to analyze various aspects of pizza sales using SQL queries. The analysis includes retrieving the total number of orders, calculating total revenue, identifying the highest-priced pizza, and more. The goal is to gain insights into sales performance and customer preferences.  
## 1.2 Objectives  
The main objectives of this project are:  
1. To retrieve specific data from the Pizza sales database based on given criteria.
2. To optimize SQL queries for better performance and accuracy.
3. To provide insights and reports based on the extracted data.

# 2. Database Schema  
## 2.1 Tables Involved  
1. orders: Contains order details including order_id and order_time
2. order_details: Contains details of each order, including quantity and price.
3. pizzas: Contains information about pizzas, including pizza_id and size.
4. pizza_types: Contains information about different pizza types, including pizza_type_id, name, and category.

# 3. SQL Queries, Results and Explanation  
# Questios Tackled:  
1. Retrieve total number of orders placed
```sql
SELECT 
    COUNT(*) AS number_of_orders
FROM
    orders;
```
This query retrieves the total number of orders placed.  
```
```
2. calculate the total revenue generated from pizza sales
```sql
SELECT 
    SUM(price) AS total_price
FROM
    orders o
        JOIN
    order_details od USING (order_id)
        JOIN
    pizzas pz ON pz.pizza_id = od.pizza_id;
```
This query calculates the total revenue generated from pizza sales.  
```
```
3. identify highest prized pizza
```sql
SELECT 
    pt.name, p.price
FROM
    pizzas p
        JOIN
     pizza_types pt USING (pizza_type_id)
ORDER BY price DESC
LIMIT 1;
```
This query identifies the highest-priced pizza  
```
```
4. identify the most commmon pizza size ordered
```sql
SELECT 
    pt.name, p.size, COUNT(*) AS common_pizza_size_ordered
FROM
    orders o
        JOIN
    order_details od USING (order_id)
        JOIN
    pizzas p ON p.pizza_id = od.pizza_id
        JOIN
    pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.name , p.size
ORDER BY common_pizza_size_ordered DESC;
```
This query identifies the most common pizza size ordered  
```
```
5. List top 5 most common ordered pizza types along with their quatities
```sql
SELECT 
    pt.name,count(order_id) as quantity
FROM
    pizza_types pt
        JOIN
    pizzas p USING (pizza_type_id)
        JOIN
    order_details od ON od.pizza_id = p.pizza_id
GROUP BY pt.name
ORDER BY quantity DESC
LIMIT 5;
```
This query lists the top 5 most commonly ordered pizza types along with their quantities.
```
```
# Itermediate  
6. Find category and total quantity of each pizza ordered
```sql
SELECT 
    pt.category, pt.pizza_type_id, COUNT(order_id) AS quantity
FROM
    pizza_types pt
        JOIN
    pizzas p USING (pizza_type_id)
        JOIN
    order_details od ON od.pizza_id = p.pizza_id
        JOIN
    orders o USING (order_id)
GROUP BY pizza_type_id , pt.category
ORDER BY quantity DESC;
```
This query finds the category and total quantity of each pizza ordered.  
```
```
7. Determine the distribution of orders by hours of the day
```sql
SELECT 
    HOUR(order_time) AS Hour, COUNT(order_id) AS order_count
FROM
    orders
GROUP BY Hour;
```
This query determines the distribution of orders by hours of the day  
```
```
8. Find the category-wise distribution of pizzas
```sql
SELECT 
    pt.category, COUNT(order_id) AS order_count
FROM
    orders o
        JOIN
    order_details od USING (order_id)
        JOIN
    pizzas p ON p.pizza_id = od.pizza_id
        JOIN
    pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.category
ORDER BY order_count;
```
This query finds the category-wise distribution of pizzas.  
```
```
9. Calculate the average number of pizzas ordered per day
```sql
SELECT 
    AVG(order_count) AS average_pizza_perday
FROM
    (SELECT 
        order_date, COUNT(order_id) AS order_count
    FROM
        orders
    GROUP BY order_date
    ORDER BY order_count) AS daily_orders;
```
This query calculates the average number of pizzas ordered per day.  
```
```
10. Determine top 3 most ordered pizza types based on revenue
```sql
SELECT 
     pt.name,SUM(od.quantity * p.price) AS revenue
 FROM
     orders o
         JOIN
     order_details od USING (order_id)
         JOIN
     pizzas p ON p.pizza_id = od.pizza_id
         JOIN
     pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC
LIMIT 3;
```
This query determines the top 3 most ordered pizza types based on revenue.  
```
```
11. Calculate percentage contribution of each pizza category to total revenue
```sql
SELECT 
    pt.category,
    SUM(od.quantity * p.price) AS revenue,
    round((SUM(od.quantity * p.price) / total_revenue) * 100,2) AS percentage_total
FROM
    orders o
        JOIN
    order_details od USING (order_id)
        JOIN
    pizzas p ON p.pizza_id = od.pizza_id
        JOIN
    pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
    CROSS JOIN
    (SELECT SUM(od.quantity * p.price) AS total_revenue
     FROM orders o
     JOIN order_details od USING (order_id)
     JOIN pizzas p ON p.pizza_id = od.pizza_id) AS total_revenue_table
GROUP BY pt.category, total_revenue
ORDER BY revenue DESC;
```
This query calculates the percentage contribution of each pizza category to total revenue.  
```
```
12. Calculate percentage contribution of each pizza type to total revenue
```sql
SELECT 
    pizza_type_id,
    SUM(od.quantity * p.price) AS pizza_type_revenue,
    (SUM(od.quantity * p.price) / total_revenue_table.total_revenue) * 100 AS percentage_contribution
FROM
    orders o
        JOIN
    order_details od USING (order_id)
        JOIN
    pizzas p ON p.pizza_id = od.pizza_id
        JOIN
    pizza_types pt USING (pizza_type_id)
        CROSS JOIN
    (SELECT 
        SUM(od.quantity * p.price) AS total_revenue
    FROM
        orders o
    JOIN order_details od USING (order_id)
    JOIN pizzas p ON p.pizza_id = od.pizza_id
    JOIN pizza_types pt USING (pizza_type_id)) AS total_revenue_table
GROUP BY pizza_type_id , total_revenue_table.total_revenue;
```
This query calculates the percentage contribution of each pizza type to total revenue.  
```
```
13. Determine top 3 most ordered pizza types based on revenue for each pizza
```sql
SELECT 
    pt.pizza_type_id,
    pt.name,
    SUM(od.quantity * p.price) AS revenue_per_pizza_type
FROM
    orders o
        JOIN
    order_details od USING (order_id)
        JOIN
    pizzas p ON p.pizza_id = od.pizza_id
        JOIN
    pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.pizza_type_id , pt.name
ORDER BY SUM(od.quantity * p.price) DESC
LIMIT 3;
```
This query calculates the total revenue for each pizza type by multiplying the quantity ordered by the price of the pizza. It then groups the results by pizza type and orders them in descending order of revenue, limiting the output to the top 3 pizza types.  
```
```
14. Calculate the cumulative revenue generated over time
```sql
select order_date,sum(od.quantity*p.price) over(order by order_date) as cumulative_revenue
from orders o 
join order_details od using(order_id)
join pizzas p on p.pizza_id=od.pizza_id
join pizza_types pt on pt.pizza_type_id=p.pizza_type_id;
```
This query calculates the cumulative revenue over time by summing the revenue for each order date. The OVER clause is used to perform a cumulative sum ordered by the order date.  
```
```
15. Determine top 3 most ordered pizza types based on revenue for each category
```sql
select name,category,revenue
from
(select revenue_table.name,revenue_table.category, revenue_table.revenue, 
rank() over(partition by category order by revenue_table.revenue) as ranking
from
(select pt.name,pt.category, sum(quantity*price) as revenue
from orders o 
join order_details od using(order_id)
join pizzas p on p.pizza_id=od.pizza_id
join pizza_types pt on pt.pizza_type_id=p.pizza_type_id
group by pt.name,pt.category) AS revenue_table) as full_rank_table
where ranking<=3
```
This query determines the top 3 pizza types based on revenue for each category. It first calculates the revenue for each pizza type and category, then ranks them within each category using the RANK() function. Finally, it filters the results to include only the top 3 pizza types per category.  
```
```

 
