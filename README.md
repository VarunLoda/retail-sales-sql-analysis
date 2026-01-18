# retail-sales-sql-analysis
Retail Sales SQL Analysis Project

Overview:
This project demonstrates an end-to-end SQL workflow on retail sales data, covering:
Table creation
Data cleaning
Data exploration
Business-driven analysis
The objective is to answer real-world business questions using SQL and present insights clearly.

Dataset Description:
The dataset represents retail transaction-level data including:
Customer details
Product categories
Transaction timestamps
Sales and cost metrics

Table Schema
CREATE TABLE retail_sales (
    transactions_id INT,
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
Notes:
Primary key is not enforced for simplicity.
FLOAT is used for monetary values (DECIMAL is recommended in production systems).

Data Cleaning Approach:
Records with NULL values in critical columns were identified.
Rows containing NULLs in transactional or financial fields were removed to ensure data integrity.
No imputation was performed, as this is a transactional analytics use case.

My Analysis & Findings:
Below are the business questions answered using SQL, along with the exact queries used.

Q1. Retrieve all columns for sales made on 2022-11-05
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
Purpose:
Analyze transactions on a specific date for audits or campaign analysis.

Q2. Transactions where category is Clothing, quantity > 10, in Nov-2022
SELECT *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND quantity > 10;
Purpose:
Identify bulk clothing purchases within a specific month.

Q3. Total sales for each category
SELECT 
    category,
    SUM(total_sale) AS net_sale
FROM retail_sales
GROUP BY category;
Purpose:
Measure revenue contribution by product category.

Q4. Average age of customers who purchased from Beauty category
SELECT 
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
Purpose:
Understand customer demographics for targeted marketing.

Q5. Transactions where total sale is greater than 1000
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
Purpose:
Identify high-value or premium transactions.

Q6. Total number of transactions by gender in each category
SELECT 
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
Purpose:
Analyze gender-based purchasing behavior across categories.

Q7. Average sale per month and best-selling month in each year
SELECT 
    year,
    month,
    avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) t
WHERE rank = 1;
Purpose:
Identify peak-performing months per year using window functions.

Q8. Top 5 customers based on highest total sales
SELECT 
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
Purpose:
Identify high-value customers.

Q9. Number of unique customers per category
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
Purpose:
Measure customer reach for each category.

Q10. Orders by shift (Morning, Afternoon, Evening)
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
Purpose:
Understand time-based purchasing behavior for operational planning.

Key SQL Concepts Used:
Data cleaning with NULL handling
Aggregations (SUM, COUNT, AVG)
Date and time functions
Window functions (RANK)

Common Table Expressions (CTEs)

Business-driven filtering
