# E-Commerce_DB
E-commerce data base for trading of goods and services online

## Data Base Queries
- Design a query to suggest popular products in the same category for the same author, excluding the Purchsed product from the recommendations

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
