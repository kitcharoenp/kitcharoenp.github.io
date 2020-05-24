---
layout: post
title: "Mysql : SUM() function with group by"
published: true
categories: [mysql]
---
 [**MySQL SUM()**][1] function retrieves the sum value of an expression which has undergone a grouping operation by GROUP BY clause.

 ```
 SELECT
   name,
   SUM(CASE WHEN quantity < 0 THEN (quantity * -1) ELSE 0 END) Total_REQ,
   SUM(CASE WHEN quantity > 0 THEN (quantity * -1) ELSE 0 END) Total_RET,
   SUM(quantity * -1)  Total
  FROM `goodsmovement`
  WHERE sapnetwork = 'WON200303360'  AND contractor_id IS NULL
  GROUP BY name
  ORDER BY name ASC
 ```

[1]: https://www.w3resource.com/mysql/aggregate-functions-and-grouping/aggregate-functions-and-grouping-sum-with-group-by.php " SUM() function with group by"
