# Case Study #.1 - Danny's Diner

See challenge here: https://8weeksqlchallenge.com/case-study-1/

<kbd>![Dannys Diner image](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/585c35c8-7a71-4716-a66a-da071e8ea663)<kbd>

***

## Table of Contents

1. [Problem Statement](#1-problem-statement) 
2. [ERD - Schema & Tables](#2-erd---schema--tables)
3. [Case Study Questions](#3-case-study-questions)
4. [Solutions](#4-solutions)

***
## 1. Problem Statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent, and also which menu items are their favorite. Having this deeper connection with his customers will help him deliver a better and more personalized experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally, he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

***
## 2. ERD - Schema & Tables

### ERD

<kbd>![DD ERD](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/68787659-c54e-44bd-8e72-7f4290d63d7d)<kbd>

### Table 1

<kbd>![DD table 1](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/cc539787-4a72-45e3-a5e6-8f99db52e2fe)<kbd>

### Table 2

<kbd>![DD table 2](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/460392d9-f16e-463c-951c-8fe1071e8bf7)<kbd>

### Table 3

<kbd>![DD table 3](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/bbed5090-6adf-4227-8918-1c1023838241)<kbd>

***
## 3. Case Study Questions

1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What are the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and B have at the end of January?

Bonus question #1

Bonus question #2
***
## 4. Solutions

### 1. What is the total amount each customer spent at the restaurant?

````sql
SELECT s.customer_id,
        SUM(m.price) as "Total Spent"
FROM SALES as s
JOIN MENU as m
 ON s.product_id = m.product_id
 GROUP BY s.customer_id;
````

#### Output:

<kbd>![DD Q1](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/598f082a-3135-4129-aae9-75270cecbfd2)<kbd>

***
### 2. How many days has each customer visited the restaurant?

````sql
SELECT customer_id,
         COUNT (DISTINCT order_date) as "# of days visited"
FROM sales
GROUP BY customer_id;
````

#### Output:

<kbd>![DD Q2](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/db277d62-2817-4d39-b9af-9bc32ed09a45)<kbd>

***
### 3. What was the first item from the menu purchased by each customer?

#### 3a
````sql
SELECT 
    sales.customer_id, 
    sales.order_date, 
    menu.product_name,
    DENSE_RANK() OVER(
      PARTITION BY sales.customer_id 
      ORDER BY sales.order_date) AS rank
  FROM sales
  JOIN menu
    ON sales.product_id = menu.product_id;
````

#### Output:

<kbd>![DD Q3 a](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/75a976b8-f112-439e-ba4a-05a97885c650)<kbd>

#### 3b
````sql
WITH ordered_sales AS (
  SELECT 
    sales.customer_id, 
    sales.order_date, 
    menu.product_name,
    DENSE_RANK() OVER(
      PARTITION BY sales.customer_id 
      ORDER BY sales.order_date) AS rank
  FROM sales
  JOIN menu
    ON sales.product_id = menu.product_id
)

SELECT 
  customer_id, 
  product_name
FROM ordered_sales
WHERE rank = 1
GROUP BY customer_id, product_name;
````

#### Output:

<kbd>![DD Q3 b](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/2a14ca53-0815-4d95-9a36-aa3671fda025)<kbd>

***
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

````sql
SELECT m.product_name,
        COUNT(s.product_id) as "# of times purchased"
FROM sales as s
JOIN MENU as m
        ON s.product_id = m.product_id
GROUP BY product_name
ORDER BY "# of times purchased" DESC
LIMIT 1;
````

#### Output:

<kbd>![DD Q 4](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/7fff3bc4-a1a2-41f2-9d37-6c2bef37458d)<kbd>

***
### 5. Which item was the most popular for each customer?

#### 5a

````sql
SELECT 
    sales.customer_id, 
    menu.product_name, 
    COUNT(menu.product_id) AS order_count,
    DENSE_RANK() OVER(
      PARTITION BY sales.customer_id 
      ORDER BY COUNT(sales.customer_id) DESC) AS rank
  FROM menu
  JOIN sales
    ON menu.product_id = sales.product_id
  GROUP BY sales.customer_id, menu.product_name;
````

#### Output:

<kbd>![DD Q5 a](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/184910f6-9913-45ae-8f57-b5ab7fa1da12)<kbd>

#### 5b

````sql
WITH most_popular AS (
  SELECT 
    sales.customer_id, 
    menu.product_name, 
    COUNT(menu.product_id) AS order_count,
    DENSE_RANK() OVER(
      PARTITION BY sales.customer_id 
      ORDER BY COUNT(sales.customer_id) DESC) AS rank
  FROM menu
  JOIN sales
    ON menu.product_id = sales.product_id
  GROUP BY sales.customer_id, menu.product_name
)
SELECT 
  customer_id, 
  product_name, 
  order_count
FROM most_popular 
WHERE rank = 1
ORDER BY order_count DESC;
````

#### Output:

<kbd>![DD Q5 b](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/c6e68027-144f-40e1-972b-69fc2f9ff2e8)<kbd>

***

### 6. Which item was purchased first by the customer after they became a member?

#### 6a 

````sql
SELECT
    members.customer_id,
    members.join_date,
    sales.product_id,
    sales.order_date,
    ROW_NUMBER() OVER(PARTITION BY sales.customer_id  ORDER BY order_date ASC) AS row_num
FROM members
JOIN sales
    ON members.customer_id = sales.customer_id
    AND order_date > join_date;
````

#### Output:

<kbd>![DD Q6 a](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/9122f30e-b4c2-435f-bfa1-9a81b134f710)<kbd>

#### 6b

````sql
WITH member_join_date AS
    (SELECT
    members.customer_id,
    members.join_date,
    sales.product_id,
    sales.order_date,
    ROW_NUMBER() OVER(PARTITION BY sales.customer_id  ORDER BY order_date ASC) as row_num
FROM members
JOIN sales
    ON members.customer_id = sales.customer_id
    AND order_date > join_date)
SELECT
    customer_id,
    product_name
FROM member_join_date
JOIN menu
    ON member_join_date.product_id = menu.product_id
WHERE row_num = 1;
````

#### Output:

<kbd>![DD Q6 b](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/33dc0c5b-0e46-494a-88a2-5ed19b65130d)<kbd>

***

### 7.Which item was purchased just before the customer became a member?

#### 7a

````sql
SELECT
    members.customer_id,
    members.join_date,
    sales.product_id,
     sales.order_date,
    ROW_NUMBER() OVER(PARTITION BY sales.customer_id  ORDER BY order_date DESC) as row_num
FROM members
JOIN sales
    ON members.customer_id = sales.customer_id
    AND order_date < join_date;
````

#### Output:

<kbd>![DD Q7 a](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/2b515b91-cb9e-4537-a410-470635e62766)<kbd>

#### 7b

````sql
WITH orders_before_membership AS
    (SELECT
    members.customer_id,
    members.join_date,
    sales.product_id,
     sales.order_date,
    ROW_NUMBER() OVER(PARTITION BY sales.customer_id  ORDER BY order_date DESC) as row_num
FROM members
JOIN sales
    ON members.customer_id = sales.customer_id
    AND order_date < join_date)
SELECT
    customer_id,
    product_name
FROM orders_before_membership
JOIN menu
    ON orders_before_membership.product_id = menu.product_id
WHERE row_num = 1;
````

#### Output:

<kbd>![DD Q7 b](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/c2c4d25e-2329-494b-90bb-cfcb60d07859)<kbd>

***

###8. What is the total items and amount spent for each member before they became a member?
````sql

SELECT 
    sales.customer_id,
    COUNT(menu.product_id) as total_items,
    SUM(menu.price)as total_sales
FROM sales
JOIN members
    ON sales.customer_id = members.customer_id
    AND order_date < join_date
JOIN menu
    ON sales.product_id = menu.product_id
GROUP BY sales.customer_id;
````

#### Output:

<kbd>![DD Q8](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/da13bfbb-794b-43cd-8379-b098ebf45de6)<kbd>

***

### 9.If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
````sql
SELECT customer_id,
        SUM( CASE WHEN product_name = 'sushi' THEN price* 10* 2 ELSE price * 10 END) as "Total Points"
FROM menu as m
JOIN sales as s
ON s.product_id = m.product_id
GROUP BY customer_id;
````

#### Output:

<kbd>![DD Q9](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/b91ec310-dd0b-49ec-8797-3854d4fada61)<kbd>

***

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and B have at the end of January?

````sql
SELECT sales.customer_id,
          SUM(CASE 
            WHEN order_date BETWEEN join_date AND DATEADD('day', 6, join_date)THEN price* 10 *2
WHEN product_name = 'sushi' THEN price*10* 2 
ELSE price * 10 END) as "Total Points"                    
FROM menu 
JOIN sales
ON sales.product_id = menu.product_id
JOIN members
ON sales.customer_id = members.customer_id
WHERE order_date < (DATE_TRUNC('month', '2021-01-31'::DATE) + interval '1 month' - interval '1 day')
GROUP BY sales.customer_id;
````

#### Output:

<kbd>![DD Q10](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/97764cd5-45f1-401d-b86f-e7599578de40)<kbd>

### Bonus Questions

1.  Join All The Things

Recreate the following table output using the available data:

<kbd>![DD bonus 1](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/521cc00a-697c-4792-a919-7ec4fd7295b1)<kbd>

````sql
SELECT  sales.customer_id,
        sales.order_date,
        menu.product_name,
       menu.price,
        CASE 
        WHEN join_date > order_date THEN 'N'
        WHEN join_date <= order_date THEN 'Y'
        ELSE 'N' 
        END AS is_member
FROM sales
LEFT JOIN members
    ON sales.customer_id = members.customer_id
JOIN menu
    ON sales.product_id = menu.product_id
ORDER BY members.customer_id, sales.order_date;
````

#### Output:

<kbd>![DD bonus q1 solution](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/75eee3f9-e5b6-459c-b956-b95aa76f340a)<kbd>

2. Rank All The Things

Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

<kbd>![DD bonus 2](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/53a446e6-42ef-4221-acb5-aa15fd3df8d5)<kbd>

````sql
WITH membership_data AS (
    SELECT  sales.customer_id,
        sales.order_date,
        menu.product_name,
       menu.price,
        CASE 
        WHEN join_date > order_date THEN 'N'
        WHEN join_date <= order_date THEN 'Y'
        ELSE 'N' 
        END AS is_member
    FROM sales
    LEFT JOIN members
        ON sales.customer_id = members.customer_id
    JOIN menu
        ON sales.product_id = menu.product_id
    ORDER BY members.customer_id, sales.order_date
)
SELECT 
        *,
        CASE
            WHEN is_member = 'N' THEN NULL
            ELSE Rank() OVER( PARTITION BY customer_id, is_member ORDER BY order_date) END AS Ranking
FROM membership_data;
````

#### Output:
<kbd>![DD bonus q2 solution](https://github.com/mbellamybb/data_with_danny_8weeksqlchallenge/assets/95842597/ec255c4d-3c99-4351-ab8a-263522134988)<kbd>




