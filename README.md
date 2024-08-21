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

 
