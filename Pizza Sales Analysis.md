# 🍕 Pizza Sales Data Analysis

## 📌 Solution

### 1. Retrive the total number of orders placed?

```Sql
SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
```
**Answer:**

<img width="134" alt="image" src=https://user-images.githubusercontent.com/youruniqueid/Screenshot.png>


The Total Number of Orders Placed 21350.

***



### 2. Calculate the total revenue generated from pizza sales?

```Sql
SELECT 
    ROUND(SUM(orders_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    orders_details
        JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id;
```

### 3. Identify the highest priced pizza?

````sql
SELECT 
    *
FROM
    pizzas
ORDER BY price DESC
LIMIT 1;
-- (or)
SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
````

### 4.Identify  the most common pizza size ordered?

```Sql
SELECT 
    pizzas.size, COUNT(orders_details.quantity) AS order_count
FROM
    pizzas
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

### 5.List the top 5 most ordered pizza types along with their quantities?

```Sql

SELECT 
    pizza_types.name, SUM(orders_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
````

### 6. Join the necessary tables to find the total quantity of each pizza category ordered?

```Sql
SELECT 
    pizza_types.category,
    SUM(orders_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity DESC;
```

### 7.Determine the distribution of orders by hour of the day?

```Sql
SELECT 
    HOUR(order_time) AS order_hour,
    COUNT(orders.order_id) AS order_count
FROM
    orders
GROUP BY order_hour;
```

 
### 8.join the relevant tables to find the category wise distribution of pizzas?

```Sql
SELECT 
    pizza_types.category,
    SUM(orders_details.quantity) AS order_count
FROM
    orders_details
        JOIN
    pizzas ON orders_details.pizza_id = pizzas.pizza_id
        JOIN
    pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizza_types.category
ORDER BY order_count DESC;
```

### 9. Group the orders by date and calculate the average number of pizzas ordered per day?

```Sql
SELECT 
    ROUND(AVG(quantity), 0) AS avg_pizza_quantity
FROM
    (SELECT 
        orders.order_date, SUM(orders_details.quantity) AS quantity
    FROM
        orders
    JOIN orders_details ON orders.order_id = orders_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
````

### 10.Determine the top 3 most ordered pizza types based on revenue?

```sql
SELECT 
    pizza_types.name,
    SUM(orders_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

### 11.Calculate the percentage contribution of each pizza type to total revenue?

```Sql
SELECT 
    pizza_types.category,
    ROUND(SUM(orders_details.quantity * pizzas.price) / (SELECT 
                    ROUND(SUM(orders_details.quantity * pizzas.price),
                                2) AS total_sales
                FROM
                    orders_details
                        JOIN
                    pizzas ON pizzas.pizza_id = orders_details.pizza_id) * 100,
            2) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```

#### 12.Analyze the cimmulative revenue generated over time?

```Sql
select order_date, sum(revenue) over(order by order_date) as cimmulative_revenue from 
(select orders.order_date , sum(orders_details.quantity * pizzas.price) as Revenue from orders_details
 join pizzas on orders_details.pizza_id = pizzas.pizza_id
 join orders on orders.order_id = orders_details.order_id
 group by orders.order_date) as sales;
```

### 13.Determine th top 3 most ordered pizza types based on revenue for each pizza category?
```Sql
select name,revenue from 
(select category , name, revenue,
rank() over(partition by category order by revenue desc) as rn from
(select pizza_types.category ,pizza_types.name, sum(orders_details.quantity*pizzas.price) as revenue from pizza_types
join pizzas on pizza_types.pizza_type_id = pizzas.pizza_type_id
join orders_details on orders_details.pizza_id = pizzas.pizza_id
group by pizza_types.category ,pizza_types.name ) as a) as b where rn<=3;
````
