# Case Study #.1 - Pizza Runner

See the challenge here: https://8weeksqlchallenge.com/case-study-2/ 

<kbd>![PR logo](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/b101bfc3-039e-44a9-9120-7cbea3cbef43)<kbd>



## Table of Contents

Problem Statement
ERD - Schema & Tables
Case Study Questions
Solutions

***
## 1. Problem Statement

Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway…)

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.!

***
## 2. ERD - Schema & Tables

### ERD


<kbd>![PR ERD](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/9d3f3b0b-6fea-492e-9275-7f719d4bf4bc)<kbd>


### Table 1

<kbd>![tbl 1](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/f12794fa-75f8-4adf-ae17-bffbc51e1d40)<kbd>

### Table 2


<kbd>![tbl 2](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/0c27fbfb-efbc-48e7-ba3a-9f53cf0add9f)<kbd>



### Table 3

<kbd>![tbl 3](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/1df9f51e-b6d6-4668-a8cc-ad0c6f908b91)<kbd>



### Table 4

<kbd>![tbl 4](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/4bfff322-1970-4b71-a3aa-da897bdd6908)<kbd>



### Table 5

<kbd>![tbl 5](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/2e3974f2-71bb-4f16-8926-1e3c20ecaf68)<kbd>



### Table 6

<kbd>![tbl 6](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/1a3fb8cd-50e9-49af-8288-1e8cb65ea0ab)<kbd>


***
## 3. Case Study Questions
## A. Pizza Metrics
1. How many pizzas were ordered?
2. How many unique customer orders were made?
3. How many successful orders were delivered by each runner?
4. How many of each type of pizza was delivered?
5. How many Vegetarian and Meatlovers were ordered by each customer?
6. What was the maximum number of pizzas delivered in a single order?
7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
8. How many pizzas were delivered that had both exclusions and extras?
9. What was the total volume of pizzas ordered for each hour of the day?
10. What was the volume of orders for each day of the week?

## B. Runner and Customer Experience
1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
4. What was the average distance travelled for each customer?
5. What was the difference between the longest and shortest delivery times for all orders?
6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
7. What is the successful delivery percentage for each runner?

## C. Ingredient Optimisation
1. What are the standard ingredients for each pizza?
2. What was the most commonly added extra?
3. What was the most common exclusion?
4. Generate an order item for each record in the customers_orders table in the format of one of the following:
    Meat Lovers
    Meat Lovers - Exclude Beef
    Meat Lovers - Extra Bacon
    Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
6. For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
7. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

## D. Pricing and Ratings
1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
2. What if there was an additional $1 charge for any pizza extras? Add cheese is $1 extra
3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.
4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
    customer_id, order_id, runner_id,rating, order_time, pickup_time, Time between order and pickup, Delivery duration, Average speed, Total number of pizzas
5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?

## E. Bonus Questions
1. If Danny wants to expand his range of pizzas - how would this impact the existing data design? Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
***
## 4. Solutions

### A. Pizza Metrics

### 1. How many pizzas were ordered?


````sql
SELECT COUNT (*) as "# of Pizzas Ordered"
FROM customer_orders;
````

#### Output:
![A1](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/650555d2-9270-486b-9d15-acea120ed500)


***
### 2. How many unique customer orders were made?


````sql
SELECT COUNT(DISTINCT order_id) as "# of Customer Orders"
FROM customer_orders;
````

#### Output:
<kbd>![A2](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/217245e0-32fa-454b-ae8f-663b4de30da5)<kbd>




***
### 3. How many successful orders were delivered by each runner?

````sql
SELECT runner_id,
        COUNT(DISTINCT order_id) as "successful orders"
FROM runner_orders
WHERE pickup_time <> 'null'
GROUP BY runner_id;

````

#### Output:
<kbd>![A3](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/8073c3fb-4a1c-4238-aedd-1957a9358654)<kbd>


***
### 4. How many of each type of pizza was delivered?

````sql
SELECT pn.pizza_name,
        COUNT(*) as "# of Pizzas Delivered"
FROM customer_orders as co
JOIN runner_orders as ro
    ON co.order_id = ro.order_id
JOIN pizza_names as pn
    ON co.pizza_id = pn.pizza_id
WHERE pickup_time <> 'null'
GROUP BY pn.pizza_name;
````

#### Output:
<kbd>![A4](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/11b68ce1-c73f-48df-98a8-3409769cd149)<kbd>


***
### 5. How many Vegetarian and Meatlovers were ordered by each customer?



````sql
SELECT customer_id, 
        SUM(CASE WHEN co.pizza_id = 1 THEN 1 ELSE 0 END) as "Meatlovers",
        SUM(CASE WHEN co.pizza_id = 2 THEN 1 ELSE 0 END) as "Vegetarian"
FROM customer_orders as co 
JOIN pizza_names as pn
ON co.pizza_id = pn.pizza_id
GROUP BY customer_id;
````

#### Output:
<kbd>![A5](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/2064cf48-9564-4482-935a-3d13f4e2a557)<kbd>


***

### 6. What was the maximum number of pizzas delivered in a single order?


````sql
SELECT co.order_id, 
    COUNT(co.order_id) as "# of Pizzas Delivered"
FROM customer_orders as co 
JOIN runner_orders as ro
    ON co.order_id = ro.order_id
WHERE pickup_time <> 'null'
GROUP BY co.order_id
ORDER BY "# of Pizzas Delivered" DESC 
LIMIT 1;
````

#### Output:
<kbd>![A6](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/707314bb-faa7-47db-ae5a-eb1d523d0af7)<kbd>




***

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````sql
SELECT order_id,
        customer_id,
        pizza_id,
        exclusions,
        extras,
        CASE
           WHEN exclusions IS NOT NULL AND exclusions <> 'null' or extras IS NOT NULL AND extras <> 'null' THEN 'CHANGE'
           ELSE 'NO CHANGE'
           END AS order_changes
        FROM customer_orders;

WITH order_review AS(
            SELECT order_id,
        customer_id,
        pizza_id,
        exclusions,
        extras,
        CASE
            WHEN exclusions IS NOT NULL AND exclusions <> 'null' or extras IS NOT NULL AND extras <> 'null' THEN 'CHANGE'
           ELSE 'NO CHANGE'
           END AS changes
        FROM customer_orders
        )
SELECT customer_id,
        changes,
        COUNT(changes) AS num_of_deliveries
FROM order_review AS o
RIGHT JOIN runner_orders as r ON o.order_id = r.order_id
WHERE r.cancellation IS NULL or r.cancellation <> 'null'
GROUP BY customer_id, changes
ORDER BY customer_id;
````

#### Output:

<kbd>![A7](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/e86748b4-acf7-4fda-9c62-42a9243a0f0f)<kbd>

***

### 8. How many pizzas were delivered that had both exclusions and extras?
````sql

SELECT COUNT(pizza_id) AS num_deliveries_w_exclusions_extras
FROM customer_orders AS c
JOIN runner_orders AS r 
    ON r.order_id = c.order_id
WHERE    pickup_time <> 'null' 
    AND (exclusions IS NOT NULL AND exclusions <> 'null' AND LENGTH(exclusions)>0)
    AND (extras IS NOT NULL AND extras <> 'null'AND LENGTH(extras)>0);
````

#### Output:
<kbd>![A8](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/639abc7b-d7b8-4255-a5b2-4f835f16817f)<kbd>

***

### 9. What was the total volume of pizzas ordered for each hour of the day?
````sql

SELECT DATE_PART('hour', order_time) AS hour_of_day,
        COUNT(order_id) AS pizza_count
FROM customer_orders
GROUP BY DATE_PART('hour',order_time);
````

#### Output:

<kbd>![A9](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/25e6e48f-5717-4c9c-9222-5fb2875fb6e7)<kbd>


***

### 10. What was the volume of orders for each day of the week?

````sql
SELECT DAYNAME(order_time),
        COUNT(order_id) AS pizzas_ordered
FROM customer_orders
GROUP BY DAYNAME(order_time);
````

#### Output:
<kbd>![A10](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/67736af0-15e1-4c25-911b-ac7029b58bee)<kbd>


### B. Runner and Customer Experience

# 1.  


````sql

````

#### Output:

