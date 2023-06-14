What issues will you address by cleaning the data?

Table : all_sessions

1.a Changing the data type of productprice to decimal 

1.b Updating the productprice by diving it by 1000000 and rounding the price to 2 decimal places

2. Changing the data type of productrevenue to decimal

3. Changing the data type of productquantity to decimal

4. Changing the data type of totaltransactionrevenue to decimal

5. Changing the data type of transactionrevenue to decimal

6. Updating totaltransactionrevenue by dividing it by 1000000

7. Updating transactionrevenue by dividing it by 1000000

8. Converting column time to a timestamp

9. Converting column timeonsite to a timestamp

10. Filling missing currencycode 

11. Deleting columns productrefundamount, itemquantity, itemrevenue and searchkeyword because they have complete null values

12. Filling nulls in column timeonsite

Table: analytics

13. Comparing visitid and visitstarttime to see if they are duplicates

14. Removing visitstarttime because it is a duplicate column of visitid

15.a Changing data type of  unitprice to float

15.b Updating the unitprice by diving it by 1000000 


16. Converting timeonsite to time format

17. Checking for null values in column userid

18. Deleting column userid because it has complete null values

19.a Changing data type of revenue column to decimal

19.b Updating the revenue by dividing it by 1000000

Table: products

20. Filling for null values in sentimentmagnitude and sentimentscore columns

Queries:
Below, provide the SQL queries you used to clean your data.

~~~sql
1.a ALTER TABLE all_sessions
  ALTER COLUMN productprice TYPE DECIMAL
  USING productprice::DECIMAL

1.b .UPDATE all_sessions
  SET productprice=productprice/1000000

  UPDATE all_sessions
  SET  productprice = ROUND                 (    (productprice::NUMERIC,2)

2. ALTER TABLE all_sessions 
   ALTER COLUMN productrevenue TYPE DECIMAL
   USING productrevenue::DECIMAL

3. ALTER COLUMN productquantity TYPE DECIMAL
   USING productquantity::DECIMAL

4. ALTER TABLE all_sessions
   ALTER COLUMN totaltransactionrevenue TYPE   DECIMAL(18,3)
   USING totaltransactionrevenue::NUMERIC (18,3)

5. ALTER TABLE all_sessions
   ALTER COLUMN transactionrevenue TYPE         DECIMAL(18,3)
   USING transactionrevenue::NUMERIC(18,3)

6.UPDATE all_sessions
  SET totaltransactionrevenue =    totaltransactionrevenue / 1000000.0

7.UPDATE all_sessions
   SET transactionrevenue = transactionrevenue /   1000000.0

8.ALTER TABLE all_sessions
ALTER COLUMN time TYPE TIME USING (TIME '00:00:00' + (interval '1 second' * time::integer))

9.ALTER TABLE all_sessions
  ALTER COLUMN timeonsite TYPE TIME USING (TIME  '00:00:00' + (interval '1 second' * timeonsite ::integer))

10. UPDATE all_sessions
    SET currencycode = 
                        CASE
                        WHEN currencycode IS NULL 
                        THEN 'USD'
                        ELSE currencycode
                        END;
11.a SELECT productrefundmount,itemquantity, itemrevenue,searchkeyword 
FROM all_sessions
WHERE productrefundmount IS NULL OR itemquantity IS NULL OR itemrevenue IS NULL OR searchkeyword IS NULL
11.b ALTER TABLE all_sessions
Drop column productrefundmount

11.c ALTER TABLE all_sessions
DROP COLUMN itemquantity

11.d ALTER TABLE all_sessions
DROP COLUMN itemrevenue

11.e ALTER TABLE all_sessions
DROP COLUMN searchkeyword

12.SET timeonsite = 
COALESCE(timeonsite, '00:00:00')
WHERE timeonsite IS NULL

13.
SELECT
COUNT(*) AS total_rows,
COUNT(DISTINCT visitid) AS Distinct_visitid,
COUNT(visitid) AS visitid,
COUNT(DISTINCT visitstarttime) AS distinct_visitstarttime,
COUNT(visitstarttime) AS visitstarttime
FROM analytics 
WHERE visitstarttime = visitid

14. Alter table analytics
    Drop column visitstarttime

15.a ALTER TABLE analytics
     ALTER COLUMN unitprice TYPE FLOAT

15.b UPDATE analytics
     SET unitprice=unitprice/1000000

16 ALTER TABLE analytics
    ALTER COLUMN timeonsite TYPE TIME 
    USING (TIME '00:00:00' + (interval '1 second' * timeonsite::integer))

17. SELECT userid 
    FROM analytics
    WHERE userid is null

18.ALTER TABLE analytics
   DROP COLUMN userid

19.a ALTER TABLE analytics
     ALTER COLUMN revenue TYPE DECIMAL(18,3)
     USING revenue::NUMERIC(18,3)

19.b UPDATE analytics
     SET revenue = revenue / 1000000.0

20.a UPDATE public.products
     SET sentimentmagnitude = CASE
     WHEN sentimentmagnitude IS NULL THEN 0 
     ELSE sentimentmagnitude 
     END
     WHERE sentimentmagnitude IS NULL

20.b UPDATE public.products
     SET sentimentscore = CASE
     WHEN sentimentscore IS NULL THEN 0 
     ELSE sentimentscore 
     END
     WHERE sentimentscore IS NULL;


~~~