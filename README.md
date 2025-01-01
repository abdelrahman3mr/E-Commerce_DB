# E-Commerce_DB
E-commerce data base for trading of goods and services online

## Data Base Queries
- Generate a daily report of the total revenue for a specific date.
   
```sql
SELECT sum(total_amount) AS DAILY_REVENUE ,
       DATE(order_date) AS ORDER_DATE
FROM orders
WHERE DATE(Order_date) = '2024-12-20'
GROUP BY order_date;
```
- Generate a monthly report of the top-selling products in a given month.
```sql
SELECT p.name,
       sum(od.quantity) AS quantity_sold,
       sum(od.quantity * od.unit_price) AS total_revenue,
       date_format(order_date, '%Y-%m') AS MONTH
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN product p ON od.product_id = p.product_id
WHERE date_format(o.order_date, '%Y-%m') = '2024-12'
GROUP BY p.product_id,
         p.name,
         MONTH
ORDER BY sum(od.quantity) DESC,total_revenue DESC;
```
- Retrieve a list of customers who have placed orders totaling more than $500 in the past month Include customer names and their total order amounts.
```sql
SELECT concat(c.first_name, ' ', c.last_name) AS CUSTOMER_NAME,
       concat('$ ', sum(total_amount)) AS TOTAL_AMOUNT,
       date_format(curdate() - interval 1 MONTH, '%Y-%m') AS Date
FROM customer c
JOIN orders o ON c.customer_id = o.order_id
WHERE o.order_date >= date_format(curdate() - interval 1 MONTH, '%Y-%m')
GROUP BY c.customer_id
HAVING sum(total_amount) > 500
ORDER BY sum(total_amount) DESC;
```
- Search for all products with the word "camera" in either the product name or description.
```sql
SELECT *
FROM product
WHERE name LIKE '%camera%'
   OR description LIKE '%camera%';
```
- Design a query to suggest popular products in the same category for the same author, excluding the Purchsed product from the recommendations
 ```sql
   SELECT p.product_id,
       p.name
   FROM product p
   LEFT JOIN order_details od -- left join to get all products including the not purchased ones
   ON p.product_id = od.product_id
   WHERE p.category_id =
       (SELECT p2.category_id
        FROM product p2
        WHERE product_id = '261') -- Get Category_id
   AND p.author_id =
       (SELECT p2.author_id
        FROM product p2
        WHERE product_id = '261') -- Get Author_id
   AND p.product_id NOT IN
       (SELECT od.product_id
        FROM orders o
        JOIN order_details od ON o.order_id = od.order_id
        WHERE o.customer_id = '18')-- Get Purchased Products
   GROUP BY p.product_id,
            p.name
   ORDER BY sum(od.quantity)DESC, sum(od.quantity*od.unit_price)
   LIMIT 5;  
