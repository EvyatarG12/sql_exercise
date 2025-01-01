@ -0,0 +1,105 @@
---
title: "sql_exercise"
format: html
editor: visual
---

## Quarto

Quarto enables you to weave together content and executable code into a finished document. To learn more about Quarto see <https://quarto.org>.

## Running Code

When you click the **Render** button a document will be generated that includes both content and the output of embedded code. You can embed code like this:

```{r}
library(DBI)
library(RSQLite)
con_chocolate <- DBI::dbConnect(drv = RSQLite::SQLite(),
                                dbname = "chocolate.sqlite")
```

Only salesreps:

```{sql, connection = con_chocolate, output.var = "salesreps"}
SELECT *
FROM salesreps
```

Only Orders:

```{sql, connection = con_chocolate, output.var = "orders"}
SELECT *
FROM orders
```

Inner join:

```{sql, connection = con_chocolate, output.var = "salesreps~orders"}
SELECT *
FROM salesreps INNER JOIN orders USING (srid)
```

Only Customers:

```{sql, connection = con_chocolate, output.var = "customers"}
SELECT *
FROM customers
```

Q1 Code:

```{sql, connection = con_chocolate, output.var = "Q1"}
SELECT salesreps.Name, SUM(orders.amount) AS total_candy_bars_sold
FROM salesreps INNER JOIN orders USING (srid)
WHERE
  orders.sale_date BETWEEN '2022-01-01' AND '2022-12-31'
    AND salesreps.year_joined = 2010
GROUP BY salesreps.Name;
```

This code combines data from the salesreps and orders tables using an inner join on the srid column, ensuring only matching rows from both tables are included. It filters the results to include only sales representatives who started in 2010 and orders in 2022, then groups the data by the sales representative's name. The final output contains the name of each sale representative and the total order amount they are associated with, called 'total_candy_bars'.

```{r , echo=FALSE}
print(Q1)
```

The final table include only three sales representatives: Tootle Naudia, al-Farrah Ghaaliba, al-Sadri Saamyya

Q2 Code: Frequency table

```{sql, connection = con_chocolate, output.var = "Q2"}
SELECT total_orders, COUNT(total_orders) AS N
FROM (SELECT COUNT(orders.cid) AS total_orders 
FROM customers INNER JOIN orders USING (cid)
GROUP BY  customers.cid) 
AS total_orders
GROUP BY total_orders

```

Q3 Code: Most sold candy first quarter of 2022

```{sql, connection = con_chocolate, output.var = "Q3_1"}
SELECT candy_names AS Candy , COUNT(*) AS number_of_times
FROM products INNER JOIN orders USING (pid)
WHERE orders.sale_date BETWEEN '2022-01-01' AND '2022-3-31'
GROUP BY products.pid
ORDER BY COUNT(*) DESC
LIMIT 1
```

The most sold candy in the first quarter of 2022 is *Coconut Crave*

Q3 Code: Name of the sales rep that sold the most of those candy bars in the second quarter of 2022

```{sql, connection = con_chocolate, output.var = "Q3_2"}
SELECT salesreps.Name , COUNT (*) AS number_of_times
FROM salesreps INNER JOIN orders USING (srid)
WHERE orders.sale_date BETWEEN '2022-04-01' AND '2022-06-30'
GROUP BY salesreps.Name
ORDER BY COUNT (*) DESC
LIMIT 1
```

Name of the sales rep that sold the most of those candy bars in the second quarter of 2022 is *Dustin Prestel*
