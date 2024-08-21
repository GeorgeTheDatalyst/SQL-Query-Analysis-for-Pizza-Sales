 # Project Report: MySQL Query Analysis for Pizza Sales  
 
![image](https://github.com/user-attachments/assets/d5e32bea-a8f2-4d2a-97e1-564a82c13779)

 ## 1. Introduction  
 ## 1.1 Project Overview  
This project aims to analyze various aspects of pizza sales using SQL queries. The analysis includes retrieving the total number of orders, calculating total revenue, identifying the highest-priced pizza, and more. The goal is to gain insights into sales performance and customer preferences.  
## 1.2 Objectives  
The main objectives of this project are:  
1. To retrieve specific data from the Pizza sales database based on given criteria.
2. To optimize SQL queries for better performance and accuracy.
3. To provide insights and reports based on the extracted data.

# 2. Database Schema  
![image](https://github.com/user-attachments/assets/a1825ddb-d5b3-4384-a673-9b82c9b69fea)

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
7. Determine the distribution of orders by hours of the day
```sql
SELECT 
    HOUR(order_time) AS Hour, COUNT(order_id) AS order_count
FROM
    orders
GROUP BY Hour;
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
14. Calculate the cumulative revenue generated over time
```sql
select order_date,sum(od.quantity*p.price) over(order by order_date) as cumulative_revenue
from orders o 
join order_details od using(order_id)
join pizzas p on p.pizza_id=od.pizza_id
join pizza_types pt on pt.pizza_type_id=p.pizza_type_id;
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
### Project Conclusion

This project aimed to analyze the order patterns, revenue, and popular items in a pizza delivery business using SQL queries. The analysis provided several key insights:

1. **Order Volume**:  
The total number of orders placed was determined, giving a clear picture of the business's order volume.

3. **Revenue Generation**:  
The total revenue generated from pizza sales was calculated, highlighting the financial performance of the business.

4. **High-Value Items**:  
The highest priced pizza was identified, which can help in understanding customer preferences for premium products.

5. **Popular Sizes**:  
The most common pizza size ordered was determined, providing insights into customer preferences regarding pizza sizes.

6. **Top Pizza Types**:   
The top 5 most commonly ordered pizza types were listed along with their quantities, indicating the most popular items on the menu.

7. **Category Analysis**:  
The total quantity of each pizza ordered by category was analyzed, helping to understand the distribution of orders across different categories.

8. **Order Timing**:  
The distribution of orders by hours of the day was examined, revealing peak ordering times and helping to optimize staffing and operations.

9. **Category-wise Distribution**:  
The category-wise distribution of pizzas was analyzed, providing a detailed view of customer preferences across different pizza categories.  
These findings offer valuable insights into customer behavior, product popularity, and revenue generation. They can be used to make informed decisions on menu adjustments, marketing strategies, and operational improvements to enhance business performance.

 
