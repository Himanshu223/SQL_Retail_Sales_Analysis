
# 🛒 SQL Project: Retail Sales Analysis  

## 📌 Project Overview  
**Project Title:** Retail Sales Analysis  
**Level:** Beginner  
**Database:** `SQL_project_P1`  

This project demonstrates **SQL data exploration, cleaning, and business analysis** using a retail sales dataset. It covers essential SQL skills used by data analysts, including data cleaning, aggregation, window functions, and solving business problems.  

---

## 🎯 Objectives  
1. **Database Setup** – Create and populate a retail sales database.  
2. **Data Cleaning** – Handle null values and remove invalid records.  
3. **Exploratory Data Analysis (EDA)** – Explore dataset size, customers, and categories.  
4. **Business Analysis** – Answer key business questions using SQL queries.  

---

## 📂 Project Structure  

### 1. Database Setup  
```sql
CREATE DATABASE SQL_project_P1;

USE SQL_project_P1;

CREATE TABLE retail_sales (
    transaction_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```  

---

### 2. Data Exploration & Cleaning  

- **Row Count:**  
```sql
SELECT COUNT(*) FROM retail_sales;
```  

- **Unique Customers:**  
```sql
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
```  

- **Distinct Categories:**  
```sql
SELECT DISTINCT category FROM retail_sales;
```  

- **Null Value Check & Deletion:**  
```sql
DELETE FROM retail_sales
WHERE sale_date IS NULL 
   OR sale_time IS NULL 
   OR customer_id IS NULL 
   OR gender IS NULL 
   OR age IS NULL 
   OR category IS NULL 
   OR quantity IS NULL 
   OR price_per_unit IS NULL 
   OR cogs IS NULL 
   OR total_sale IS NULL;
```  

---

## 📊 Data Analysis & Business Key Problems  

### Q1. Sales made on `'2022-11-05'`
```sql
SELECT * 
FROM retail_sales
WHERE sale_date = '2022-11-05';
```  

### Q2. Transactions in `'Clothing'` with quantity > 10 (Nov 2022)  
```sql
SELECT * 
FROM retail_sales
WHERE category = 'Clothing'
  AND quantity > 10
  AND YEAR(sale_date) = 2022
  AND MONTH(sale_date) = 11;
```  

### Q3. Total sales per category  
```sql
SELECT category, SUM(total_sale) AS Total_sales
FROM retail_sales
GROUP BY category;
```  

### Q4. Average age of customers in `'Beauty'` category  
```sql
SELECT AVG(age) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```  

### Q5. Transactions with sales > 1000  
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```  

### Q6. Transactions by gender in each category  
```sql
SELECT category, gender, COUNT(*) AS number_of_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```  

### Q7. Best-selling month in each year  
```sql
SELECT *
FROM (
    SELECT 
        YEAR(sale_date) AS Year,
        MONTH(sale_date) AS Month,
        ROUND(AVG(total_sale), 2) AS avg_sales,
        RANK() OVER (PARTITION BY YEAR(sale_date) 
                     ORDER BY ROUND(AVG(total_sale), 2) DESC) AS RankNo
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
) t
WHERE RankNo = 1;
```  

### Q8. Top 5 customers by total sales  
```sql
SELECT TOP 5 customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC;
```  

### Q9. Unique customers by category  
```sql
SELECT category, COUNT(DISTINCT customer_id) AS no_of_unique_customers
FROM retail_sales
GROUP BY category;
```  

### Q10. Orders by shift (Morning, Afternoon, Evening)  
```sql
WITH HourlyShift AS (
    SELECT 
        CASE 
            WHEN DATEPART(HOUR, sale_time) < 12 THEN 'Morning'
            WHEN DATEPART(HOUR, sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT shift, COUNT(*) AS no_of_orders
FROM HourlyShift
GROUP BY shift;
```  

---

## 📈 Key Insights  
- Afternoon is the **busiest sales shift**.  
- Certain months consistently **outperform others** → seasonal effect.  
- Customers in the **Beauty category** are slightly younger on average.  
- Top 5 customers drive a significant share of revenue.  
