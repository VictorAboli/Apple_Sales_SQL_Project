#  Apple Sales Data Analysis using SQL

<p align="center">
  <img src="[images/ERD.png](https://github.com/VictorAboli/Apple_Sales_SQL_Project/blob/ea6ce622790532f12a752fe47874dd2b7c9ccc3f/Apple_Image.webp)" width="700">
</p>
## **📌 Project Overview**

This project presents an end-to-end **SQL Data Analysis** of an Apple retail sales dataset containing **over 1 million records** distributed across **5 relational tables**.

The objective of this project was to perform **Exploratory Data Analysis (EDA)**, solve real-world **business problems**, uncover **historical sales trends**, and generate **data-driven business recommendations** for stakeholders.

The project demonstrates practical SQL skills commonly used by Data Analysts to transform raw transactional data into meaningful business insights.

Data Source: Kaggle = https://www.kaggle.com/datasets/amangarg08/apple-retail-sales-dataset

---

## **📊 Dataset Information**

| **Feature** | **Details** |
|-------------|-------------|
| **Database** | Apple Sales Database |
| **Records** | 1,000,000+ |
| **Tables** | 5 |
| **Database Type** | Relational |
| **Analysis Tool** | Microsoft SQL Server |
| **SQL Language** | T-SQL |

---

<p align="center">
  <img src="[images/ERD.png](https://github.com/VictorAboli/Apple_Sales_SQL_Project/blob/802402b3d183f4a5c751f74925dd2196a980985e/Apple%20ERD%20Diagram.png)" width="700">
</p>

---

## **📁 Database Structure**

The project consists of the following tables:

- **Sales**
- **Products**
- **Stores**
- **Category**
- **Warranty**

These tables are connected through primary and foreign keys to support comprehensive business analysis.

---

## **🎯 Project Objectives**

✔ Perform Exploratory Data Analysis (EDA)

✔ Solve real-world business problems using SQL

✔ Analyze sales performance across stores and countries

✔ Evaluate warranty claims and product quality

✔ Study product launch performance

✔ Discover historical sales trends

✔ Generate actionable business recommendations


---

# **📈 Business Questions Solved**

The project answers **20+ business questions**, including:

- Number of stores by country
- Total units sold by each store
- Monthly sales analysis
- Warranty claim analysis
- Best-selling stores
- Product performance
- Product launch analysis
- Country-wise sales comparison
- Year-over-Year (YoY) Growth
- Running Total Sales
- Product lifecycle analysis
- Warranty rejection percentage
- Store performance ranking
- Product price vs warranty claims
- Sales trends over time

---

## **📊 Key Insights**

✅ United States contains the highest number of Apple Stores.

✅ Melbourne Apple Chadstone recorded the highest units sold.

✅ December 2023 generated one of the highest sales periods.

✅ Approximately **24%** of warranty claims were rejected.

✅ Accessories generated the highest warranty claims.

✅ High-priced products received the largest number of warranty claims.

✅ Running sales increased consistently over the four-year period.

✅ Several products continued generating strong sales even after 18 months from launch.

---

## **💼 Business Recommendations**

- Increase inventory allocation for high-performing stores.

- Improve supply chain planning for peak sales months.

- Investigate products with frequent warranty claims.

- Monitor newly launched products closely.

- Improve quality assurance for expensive products.

- Increase marketing efforts in underperforming regions.

- Optimize inventory using historical sales trends.

- Improve customer retention through better warranty support.

---

## **🚀 Skills Demonstrated**

- SQL
- Data Cleaning
- Exploratory Data Analysis (EDA)
- Business Intelligence
- Relational Database Design
- Window Functions
- Data Aggregation
- Business Problem Solving
- Data Interpretation
- Analytical Thinking

---

## ⭐ If you found this project useful, consider giving it a Star!


DIMENSION EXPLORATION 
```SQL
SELECT DISTINCT repair_status 
FROM dbo.warranty_;
SELECT DISTINCT Product_Name
FROM dbo.products_
SELECT DISTINCT City
FROM dbo.stores_
SELECT DISTINCT category_name
FROM dbo.category_
```
DATE EXPLORATION 
```SQL
SELECT MAX (Launch_Date) 
FROM dbo.products_
SELECT MIN(Price) 
FROM dbo.products_
SELECT 
DATEDIFF(day,'2020-01-01','2024-11-04') 
FROM products_
```
MEASURES EXPLORATION 
```SQL
SELECT COUNT(Product_ID)
FROM products_
SELECT SUM(price)
FROM products_
SELECT SUM(quantity)
FROM sales_
```
Magnitude Analysis 
TOTAL NUMBER OF SALES  BY COUNTRY 
```SQL
SELECT 
COUNT(s.quantity) AS COUNT_OF_SALES,
t.Country 
FROM sales_ s
LEFT JOIN stores_ t
ON s.store_id = t.Store_ID
GROUP BY t.Country
ORDER BY COUNT(s.quantity) DESC
--TOTAL QUANTITY BY CATEGORY 
SELECT 
c.category_name,
COUNT(s.quantity) AS Count_of_Quantity
FROM category_ c
JOIN products_ p
ON c.category_id = p.Category_ID
JOIN sales_ s
ON p.Product_ID = s.product_id
GROUP BY c.category_name
ORDER BY COUNT(s.quantity) DESC
-- AVERAGE PRICE BY PRODUCT 
SELECT 
AVG (Price) AS Avg_Price,
Product_Name
FROM products_
GROUP BY Product_Name
ORDER BY AVG (Price) DESC
```
--RANKING ANALYSIS 
```SQL
--RANK TOP 5 COUNTRIES BY PRICE (WINDOW FUNCTION)
SELECT 
t.country,
RANK() OVER(PARTITION BY p.Price ORDER BY  t.Country)
FROM stores_ t
JOIN sales_ s
ON t.Store_ID = s.store_id 
JOIN products_ p
ON p.Product_ID = s.product_id
GROUP BY t.country

SELECT TOP 5
t.country,
DENSE_RANK() OVER(ORDER BY SUM(P.Price * s.quantity)DESC) AS RANK_NUMBER
FROM stores_ t
JOIN sales_ s
ON t.Store_ID = s.store_id 
JOIN products_ p
ON p.Product_ID = s.product_id
GROUP BY t.country

--TOP 5 PRODUCT FOR QUANTITY 
SELECT 
p.product_name,
SUM(quantity),
DENSE_RANK() OVER(ORDER BY SUM(s.quantity)DESC) AS Rank_Number 
FROM Products_ p
JOIN sales_ s
ON p.Product_ID = s.product_id
GROUP BY p.product_name
```


-- Solving Business Problems
--Medium problems

Q1. Find the number of stores in each country 
```SQL
SELECT 
COUNT(*) AS Number_of_Stores,
Country
FROM Stores_
GROUP BY Country 
ORDER BY COUNT(*) DESC
```
Q2.Calculate the total number of units sold by each store
```SQL
SELECT 
  SUM(s.quantity) AS Number_of_Units,
  t.Store_Name,
  t.city
  FROM Sales_ s
  JOIN Stores_ t
  ON t.Store_ID = s.Store_id
GROUP BY t.Store_Name, t.city
ORDER BY SUM(s.quantity) desc
```

Q3. Identity how many sales occurred in Decemeber 2023.
```SQL
SELECT 
  COUNT(sale_id) AS total_sales
  FROM Sales_
WHERE FORMAT(Sale_date, 'yyyy-MM') = '2023-12'
```
Q4. Determine how many stores have never had a warranty claim filed.
```SQL
SELECT 
Store_Name,
COUNT(Claim_id) as Number_of_Claim
FROM(
    SELECT 
    t.Store_ID,
    t.Store_Name,
    w.Claim_id
    FROM Stores_ t
    LEFT JOIN Sales_ s
    ON t.Store_ID = s.Store_id 
    LEFT JOIN warranty_ w
    ON w.Sale_id = s.sale_id
)t
WHERE Claim_id IS NULL
GROUP BY Store_Name
-- Another step 
SELECT COUNT(DISTINCT st.store_id) AS Stores_With_No_Warranty_Claims
FROM Stores_ st
LEFT JOIN Sales_ s
    ON st.store_id = s.store_id
LEFT JOIN Warranty_ w
    ON s.sale_id = w.sale_id
WHERE w.sale_id IS NULL;
```
Q5 Calculate the percentage of warranty claims marked as "REJECTED"
```SQL
SELECT
CONCAT(COUNT(claim_id) * 100 /(SELECT COUNT(*)FROM warranty_ ),'%') AS Percentage_of_Rejected
FROM warranty_
WHERE repair_status = 'Rejected'
```
Q6. Identify which store had the highest total units sold in the last year.
```SQL
SELECT TOP 1
    t.Store_Name,
    SUM(s.quantity) AS total_units,
    YEAR(GETDATE()) - 2 AS previous_year
    FROM stores_ t
    JOIN sales_ s 
    ON t.Store_ID = s.Store_id
WHERE DATEPART(year,s.Sale_date) >= YEAR(GETDATE()) - 2
GROUP BY t.Store_Name 
ORDER BY SUM(s.quantity) desc
```
Q7. Count the number of unique products sold in the last year
```SQL
SELECT 
    COUNT(DISTINCT p.Product_ID) AS UniqueProducts,
    SUM(s.quantity) AS Total_Products_Sold,
    YEAR(GETDATE()) - 2 AS Year_
    FROM Products_ p
    JOIN Sales_ s 
    ON p.Product_ID = s.Product_id 
WHERE DATEPART(YEAR, s.Sale_date) = YEAR(GETDATE()) - 2
```
Q8.Find the Average price of products in each category 
```SQL
SELECT 
    p.category_id,
    c.category_name,
    AVG(p.Price) as avg_price
    FROM Products_ p 
    JOIN Category_ c
    ON p.Category_ID = c.category_id
GROUP BY 
p.category_id,
c.category_name
ORDER BY AVG(p.Price) desc
```
Q9. How many warranty claims were filled in May?
```SQL
SELECT 
COUNT(*) AS Month_of_May
FROM warranty_
WHERE DATEPART(MONTH,claim_date) = '05'
```
Q10.For Each Store, Identify the best selling day based on Highest quantity Sold.
```SQL
SELECT 
*
FROM(
    SELECT 
    Store_id,
    SUM(quantity) AS Total_quantity,
    DATENAME(WEEKDAY,Sale_date) AS Weekdays,
    DENSE_RANK()OVER(PARTITION BY Store_id ORDER BY Store_id,SUM(quantity)) rank_
    FROM Sales_
    GROUP BY Store_id,DATENAME(WEEKDAY,Sale_date)
    )t
WHERE rank_ = 1
```
**Meduim Level Question
**Q11.Identify the least selling product in each country based on Total units sold.

```SQL
SELECT 
*
FROM(
SELECT 
  t.Country,
  SUM(s.quantity) AS Total_qty_Sold,
  p.Product_Name,
  RANK() OVER(PARTITION BY t.Country Order BY SUM(s.quantity)) as Rank_
  FROM Sales_ s
  JOIN Stores_ t
  ON t.Store_ID = s.Store_id
  JOIN Products_ p
  ON p.Product_ID = s.Product_ID 
  GROUP BY t.Country,p.Product_Name
)t
WHERE Rank_ = 1;
```
Q12. Calculate how many warrant claims were filed within 180 days of a product sale.
```SQL
SELECT 
COUNT(*)
    FROM warranty_ w
    LEFT JOIN sales_ s
    ON s.sale_id = w.sale_id
WHERE 
DATEDIFF(day, s.sale_date, w.claim_date) <= 180
```
Q13. Determine how many warranty claims were filed for products launched in the last 2 years 
```SQL
SELECT 
  p.Product_Name,
  COUNT(s.sale_id) AS Count_,
  COUNT(w.claim_id) AS No_claim
  FROM warranty_ w 
  RIGHT JOIN Sales_ s
  ON w.Sale_id = s.sale_id 
  JOIN products_ p
  ON p.Product_ID = s.product_id
WHERE
p.Launch_Date >= DATEADD(YEAR, -2, GETDATE())
GROUP BY p.Product_Name
ORDER BY COUNT(w.claim_id) desc
```
Q14. List the months in the last three years where sales exceeded 5,000 units in the USA.
```SQL
SELECT
SUM(s.quantity) AS Total_count,
FORMAT(s.sale_date,'yyyy,MM') AS Date_
    FROM Sales_ s
JOIN Stores_ t
ON s.Store_id = t.Store_ID
WHERE 
     t.Country = 'United States' AND 
     s.sale_date >= DATEADD(year, -3, GETDATE())
GROUP BY FORMAT(s.sale_date,'yyyy,MM') 
HAVING SUM(s.quantity) > 5000
ORDER BY FORMAT(s.sale_date,'yyyy,MM')
```
Q15. Identify the product category with the most warranty claims filed in the last two years
```SQL
SELECT 
     c.Category_name,
     COUNT(w.claim_id) AS No_of_Claims,
     DATEPART(YEAR,w.Claim_date) AS years
FROM warranty_ w
LEFT JOIN sales_ s
ON w.sale_id = s.sale_id
JOIN products_ p
ON p.Product_ID = s.product_id
JOIN category_ c
ON c.category_id = p.Category_ID
WHERE DATEPART(YEAR,w.Claim_date) >= YEAR(GETDATE()) - 3
GROUP BY c.category_name,DATEPART(YEAR,w.Claim_date)
ORDER BY COUNT(w.claim_id) DESC 
```

**Complex(5 Questions)
**
Q16. Determine the percentage chance of recieving warranty claims after each purchase for each country.
```SQL
SELECT 
t.Country,
Total_unit_sold,
No_of_Count,
CAST((No_of_Count * 100.0 /Total_unit_sold) AS DECIMAL(10,2)) AS Percentage_
FROM (
    SELECT 
    t.Country,
    SUM(s.quantity) AS Total_unit_sold,
    COUNT(w.claim_id) AS No_of_Count
    FROM sales_ s
    JOIN stores_ t
    ON s.store_id = t.Store_ID
    LEFT JOIN warranty_ w
    ON w.Sale_id = s.sale_id 
    GROUP BY t.Country
)t
ORDER BY CAST((No_of_Count * 100.0 /Total_unit_sold) AS DECIMAL(10,2)) DESC
```
Q17. Analyze the year-by-year growth ratio for each store.
```SQL
WITH Previous_year
as
 (   SELECT 
          t.Store_ID,
          t.Store_Name AS Store_name,
          FORMAT(s.sale_date,'yyyy') AS YEAR_,
          SUM(s.quantity * p.Price) AS total_sales
    FROM stores_ t
    JOIN Sales_ s
    ON t.Store_ID = s.store_id
    JOIN products_ p
    ON p.Product_ID = s.product_id
    GROUP BY  t.Store_Name,
           FORMAT(s.sale_date,'yyyy'),t.Store_ID
   ),
 table_2 AS (
SELECT 
          Store_Name,
          YEAR_,
          total_sales,
LAG(total_sales) OVER(PARTITION BY  Store_Name ORDER BY YEAR_ ) AS Previous_sales
FROM Previous_year
)
SELECT 
          Store_Name,
          YEAR_,
          total_sales,
          Previous_sales,
CAST(((total_sales - Previous_sales)* 100.0) / Previous_sales AS DECIMAL(10,2))AS YoY
FROM table_2
WHERE Previous_sales IS NOT NULL 
```
Q18. Calculate the correlation between product price and warranty claims for products sold, Segmented by price range.
```SQL
SELECT 
CASE WHEN p.price < 500 THEN 'Less Exp'
     WHEN p.price BETWEEN 500 AND 1000 THEN 'Mid-Range'
     ELSE 'High Exp'
END AS Price_segmentation,
COUNT(w.claim_id) AS Count_claims
FROM warranty_ w
LEFT JOIN sales_ s
ON s.sale_id = w.sale_id
JOIN products_ p
ON p.Product_ID = s.product_id
GROUP BY
CASE WHEN p.price < 500 THEN 'Less Exp'
     WHEN p.price BETWEEN 500 AND 1000 THEN 'Mid-Range'
     ELSE 'High Exp'
END 
ORDER BY 
CASE WHEN p.price < 500 THEN 'Less Exp'
     WHEN p.price BETWEEN 500 AND 1000 THEN 'Mid-Range'
     ELSE 'High Exp'
END
```
Q19. Identify the store with the highest percentage of "Completed" claims relative to total claims filed.
```SQL
WITH Base_query AS
(   SELECT  
    t.Store_ID,
    w.repair_status,
    COUNT(w.claim_id) AS Repaired_Completed
    FROM warranty_ w
    LEFT JOIN sales_ s
    ON s.sale_id = w.sale_id
    JOIN stores_ t 
    ON s.store_id = t.Store_ID
    WHERE w.repair_status = 'Completed'
    GROUP BY t.Store_ID,
    w.repair_status
),
Base_query_2 AS
(   SELECT  
    t.Store_ID,
    COUNT(w.claim_id) AS Total_Repaired
    FROM warranty_ w
    LEFT JOIN sales_ s
    ON s.sale_id = w.sale_id
    JOIN stores_ t 
    ON s.store_id = t.Store_ID
    GROUP BY t.Store_ID   
)
SELECT 
     t1.Store_Name,
     b1.Repaired_Completed,
     b2.Total_Repaired,
     CONCAT(CAST((b1.Repaired_Completed * 100 )/ b2.Total_Repaired  AS DECIMAL (10,2)), '%') as Percentage_
     FROM Base_query b1
     JOIN Base_query_2 b2
     ON b1.Store_ID = b2.Store_ID
     JOIN stores_ t1
     ON b1.Store_ID = t1.Store_ID
   ORDER BY  CONCAT(CAST((b1.Repaired_Completed * 100 )/ b2.Total_Repaired  AS DECIMAL (10,2)), '%') DESC
```
Q20. Write a query to calculate the monthly running total of sales for each store over the past four years and compare trends during this period.
```SQL
SELECT 
     Store_,
     year_,
     Month_,
     Total_sales,
     SUM (Total_sales) OVER  (PARTITION BY Store_ ORDER BY year_,Month_ ) AS running_sales
FROM
(
SELECT 
    s.store_id as Store_,
    YEAR(s.Sale_date) as year_,
    MONTH(s.Sale_date) as Month_,
    SUM(p.Price * s.quantity) AS Total_sales 
    FROM sales_ s
    JOIN Products_ p
    ON s.product_id = p.Product_ID
GROUP BY 
    s.store_id,YEAR(s.Sale_date),
    MONTH(s.Sale_date)
)t
```
Q21. Analyze product sales trends over time, segmented into key period:from the launch to 6 months, 6-12 months, 12-18 months, and beyond 18 months.
```SQL
SELECT 
    p.Product_name,
CASE
        WHEN s.Sale_date <= DATEADD(MONTH, 6, p.Launch_Date) 
            THEN '0-6 Months'
        WHEN s.Sale_date <= DATEADD(MONTH, 12, p.Launch_Date) 
            THEN '6-12 Months'
        WHEN s.Sale_date <= DATEADD(MONTH, 18, p.Launch_Date) 
            THEN '12-18 Months'
        ELSE 'Beyond 18 Months'
    END AS Period_,
    SUM(s.quantity) AS total_sales
FROM sales_ s
JOIN Products_ p
ON s.Product_id = p.Product_ID
GROUP BY  p.Product_name,
CASE
        WHEN s.Sale_date <= DATEADD(MONTH, 6, p.Launch_Date) 
            THEN '0-6 Months'
        WHEN s.Sale_date <= DATEADD(MONTH, 12, p.Launch_Date) 
            THEN '6-12 Months'
        WHEN s.Sale_date <= DATEADD(MONTH, 18, p.Launch_Date) 
            THEN '12-18 Months'
        ELSE 'Beyond 18 Months'
    END



## **📌 Conclusion**

This project demonstrates how SQL can be used to analyze large-scale transactional data, solve complex business problems, identify historical trends, and provide actionable recommendations for improving sales performance, inventory management, and overall business decision-making.

ORDER BY  p.Product_name,
    SUM(s.quantity)
```



