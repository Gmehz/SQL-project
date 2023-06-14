What are your risk areas? Identify and describe them.

1. In order to perform some analysis from the products table and the sales_report table I want to make sure if in fact they have a link through productsku and sku and if I can change the column name sku to productsku
2. The data set contain huge amount of null values in the all_sessions table. I wanted to write a formula to calculate productrevenue by multiplying productprice and productquantity since productprice is available in the table and productrevenue and productquantity columns are almost null. 



QA Process:
Describe your QA process and include the SQL queries used to execute it.

1.

Validate the number of products
~~~sql
SELECT COUNT (DISTINCT productsku)
FROM
(SELECT srp.productsku AS productsku, srp.name AS name, p.name AS productname
 FROM sales_report AS srp
 INNER JOIN products AS p
 ON srp.productsku=p.sku)tmp

~~~
Retrieve total number of products from sales_report
~~~sql
SELECT COUNT(sku) FROM products
~~~
The results differ and I can conclude that productsku and sku are not the same.

2. Retrieving productquantity, productprice and productrevenue where they are not null to see if my reasoning to create a formula will work 
~~~sql
SELECT productquantity, productprice, productrevenue
FROM all_sessions
WHERE productquantity IS NOT NULL AND productrevenue IS NOT NULL
~~~
This query return me only 4 rows with complete values for productquantity, productprice and productrevenue. However only 1 row have corresponding values that would fit my formula. So, I concluded that even if I retrieve the productquantity from another table which is also less likely because the data set is so confusing and few links are available to connect the tables together, I would not be able to fill the null values in those columns with my formula.