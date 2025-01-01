# E-Commerce_DB
E-commerce data base for trading of goods and services online

## Data Base Queries
 - Generate a daily report of the total revenue for a specific date.
   
```sql
select sum(total_amount) from orders where order_date = '2024-12-20';
```
 - Generate a monthly report of the top-selling products in a given month.
```sql
select 
	p.name ,
    p.stock_quantity,
   sum(od.quantity) as quantity_sold,
   sum(od.quantity * od.unit_price) as total_revenue
 from 
orders o join order_details od
on o.order_id = od.order_id
join product p on od.product_id = p.product_id 
where o.order_date like '2024-12%'
group by p.product_id, p.name, p.stock_quantity
order by sum(od.quantity) Desc,total_revenue desc;
```
- Retrieve a list of customers who have placed orders totaling more than $500 in the past month Include customer names and their total order amounts.
```sql
select c.customer_id,sum(total_amount) ta from customer c
join orders o on c.customer_id = o.order_id 
group by c.customer_id
having ta > 500
order by ta desc;
```
- Search for all products with the word "camera" in either the product name or description.
```sql
select * from product where name or description like '%camera%';
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
