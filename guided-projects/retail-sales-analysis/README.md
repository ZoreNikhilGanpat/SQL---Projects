# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Database**: RETAIL

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named 'RETAIL'.
- **Table Creation**: A table named 'SALES' is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.
- However, I directly imported the data through Excel rather than creating tables.

### 2. Data Exploration & Cleaning

- **Column Renaming and Datatype Changing**:Since data being imported , changing to appropriate datatypes like `TIME`, `DATE` and column names as well.
- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.
- **Storing original copy of data in `SALES_OG` table for data retrieval.**

```sql
ALTER TABLE SALES 
CHANGE ï»¿transactions_id  TRANS_ID INT ;

UPDATE SALES
SET SALE_DATE = STR_TO_DATE(SALE_DATE,'%d-%m-%Y');

ALTER TABLE SALES 
MODIFY SALE_DATE DATE;

ALTER TABLE SALES
MODIFY COLUMN SALE_TIME TIME;

ALTER TABLE SALES
CHANGE CUSTOMER_ID CUST_ID INT,
CHANGE GENDER GENDER VARCHAR(10),
CHANGE AGE AGE INT,
CHANGE CATEGORY CATEGORY VARCHAR(30),
CHANGE QUANTIY QUANTITY INT,
CHANGE PRICE_PER_UNIT PRICE_PER_UNIT INT,
CHANGE COGS COGS INT,
CHANGE TOTAL_SALE TOTAL_SALE INT;


ALTER TABLE SALES
MODIFY COLUMN TRANS_ID INT PRIMARY KEY;

SELECT COUNT(*) FROM SALES;
SELECT COUNT(*) FROM SALES_OG;

SELECT * 
FROM SALES 
WHERE SALE_DATE IS NULL
   OR SALE_TIME IS NULL
   OR CUST_ID IS NULL
   OR AGE IS NULL
   OR GENDER IS NULL
   OR CATEGORY IS NULL
   OR QUANTITY  IS NULL
   OR PRICE_PER_UNIT  IS NULL
   OR COGS IS NULL
   OR TOTAL_SALE IS NULL;

SELECT COUNT(*)
FROM SALES ;

SELECT COUNT(DISTINCT CUST_ID)
FROM SALES ;

SELECT  DISTINCT CATEGORY
FROM SALES;

SELECT GENDER, COUNT(TRANS_ID)
FROM SALES
GROUP BY GENDER;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT *
FROM SALES
WHERE SALE_DATE ='2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT *,ROW_NUMBER() OVER()
FROM SALES 
WHERE CATEGORY ='Clothing'AND QUANTITY>=4 AND YEAR(SALE_DATE)=2022 AND MONTH(SALE_DATE)=11;
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT  CATEGORY,SUM(TOTAL_SALE) AS TOTAL 
FROM SALES
GROUP BY CATEGORY;
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT CATEGORY , AVG(AGE)
FROM SALES
WHERE CATEGORY ='BEAUTY';
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT COUNT(TRANS_ID) AS SALES_OVER_K 
FROM SALES 
WHERE TOTAL_SALE>1000;
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT GENDER , COUNT(TRANS_ID)
FROM SALES 
GROUP BY GENDER;
```

7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
WITH MONTHLY_SALES AS(
	SELECT YEAR(SALE_DATE) AS YEARS,
		MONTH(SALE_DATE) AS MONTHS,
		AVG(TOTAL_SALE)AS AVG_SALE
	FROM SALES
    GROUP BY YEARS , MONTHS
)
SELECT YEARS , MONTHS , AVG_SALE
FROM (
	SELECT 
		    YEARS,
        MONTHS,
        AVG_SALE,
        ROW_NUMBER() OVER(PARTITION BY YEARS ORDER BY AVG_SALE DESC) AS RN
	FROM MONTHLY_SALES
) AS AGGTABLE
WHERE RN=1;
```

8. **Write a SQL query to find the top 5 customers based on the highest total sales **:
```sql
SELECT CUST_ID , SUM(TOTAL_SALE) AS TOTALS
FROM  SALES
GROUP BY CUST_ID 
ORDER BY TOTALS  DESC LIMIT 5;
```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT CATEGORY ,SUM(DISTINCT (CUST_ID))
FROM SALES 
GROUP BY CATEGORY;
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
SELECT 
	CASE
		WHEN HOUR(SALE_TIME) BETWEEN 6 AND 11 THEN  'MORNING'
      WHEN HOUR(SALE_TIME) BETWEEN 12 AND 17 THEN 'AFTERNOON'
      WHEN HOUR(SALE_TIME) BETWEEN 18 AND 23 THEN 'EVENING'
      ELSE 'NIGHT'
	END AS SHIFT
    COUNT(TRANS_ID)
FROM SALES
GROUP BY SHIFT
ORDER BY COUNT(TRANS_ID) DESC ;

```
## Findings

- **Customer Demographics**: The dataset includes customers and sales across age groups, with categories 'Clothing','Beauty','Electronics'.
- **High-Value Transactions**: Significant transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.
