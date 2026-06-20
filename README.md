
# Retail Sales Analysis & Exploration (SQL)

##  Project Overview  ##

This project transforms raw retail data into actionable business intelligence by combining backend database engineering with interactive frontend visualizations. Project involves a comprehensive Exploratory Data Analysis (EDA) and data analysis on a retail sales dataset using PostgreSQL and pgAdmin 4. The objective is to set up the database, perform critical data cleaning to handle missing values, explore core database dimensions, and write advanced SQL queries to extract key business insights.

The analysis covers sales trends, customer demographics, transaction patterns across daily shifts, and product category performance

Using PostgreSQL for data cleaning and advanced querying alongside Power BI for dynamic data modeling and dashboarding, the analysis tracks key performance metrics across sales trends, customer demographics, product categories, and operational shift patterns.

## 📂 Project Structure
### 1. Database Setup
Table Creation: Setting up the database schema and defining data types for the retail dataset.

Verification: Running baseline counts and limit tests to confirm proper data ingestion.

### 2. Data Cleaning
Null Value Assessment: Inspecting specific constraint failures and executing an all-inclusive query to isolate incomplete records across all columns.

### 3. Data Exploration
Database Profiling: Calculating basic metadata metrics like total revenue data points, unique customer counts, and unique item categories.

### 4. Data Analysis & Business Insights
Targeted Business Queries: Developing specific SQL queries (Q1–Q10) to solve complex analytical problems using conditional logic (CASE WHEN), window functions (RANK() OVER), date parsing (EXTRACT, TO_CHAR), and aggregations.


## ** 💻 SQL Scripts & Implementation ** ##

**🛠️ Database Setup & Table Creation**
```sql
--Create Table 
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales(
transactions_id	INT PRIMARY KEY,
sale_date DATE,
sale_time	TIME,
customer_id	INT,
gender	VARCHAR(20),
age	INT,
category VARCHAR(20),	
quantity	INT,
price_per_unit FLOAT,	
cogs	FLOAT,
total_sale FLOAT
);
```

```sql
--TO VIEW THE  FIRST 10 ROWS
SELECT * FROM retail_sales
LIMIT 10;


--COUNT THE TOTAL NUMBER OF ROWS
SELECT COUNT(*) FROM retail_sales;
```

**🧹 2. Data Cleaning**
```sql
--VIEW THE NULL VALUES (By Manually Testing Each Constraint)
SELECT COUNT(*) FROM retail_sales
WHERE transactions_id IS NULL;
```

```sql
--NULL Values (By Common Command)
SELECT * FROM retail_sales
WHERE (
	transactions_id IS NULL
	OR
	sale_date IS NULL
	OR
	sale_time IS NULL
	OR
	customer_id IS NULL
	OR 
	gender IS NULL
	OR 
	age IS NULL
	OR
	category IS NULL
	OR 
	quantity IS NULL
	OR
	price_per_unit IS NULL
	OR 
	cogs IS NULL
	OR
	total_sale IS NULL
	);
```

**📊 3. Data Exploration**
```sql
--How many sales we have?
SELECT COUNT(*)
AS total_sale FROM retail_sales;

--How many UNIQUE customers we have?
SELECT COUNT(DISTINCT customer_id) 
AS total_sales FROM retail_sales;

--How many UNIQUE category we have
SELECT DISTINCT category FROM retail_sales;  
```

**💡 4. Data Analysis & Business Insights**
**➡️Q1. Write a SQL query to retrieve all columns for sales made on '2022-11-05.**
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
````


**➡️Q2. Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022.**
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4;
```


**➡️Q3. Write a SQL query to calculate the total sales (total_sale) for each category.**
```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1;
```


**➡️Q4. Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**
```sql
SELECT 
	Round(AVG(age),2) as avg_age
	FROM retail_sales
	where category = 'Beauty';
```


**➡️Q5. Write a SQL query to find all transactions where the total_sale is greater than 1000.**
```sql
SELECT 
	* 
	FROM retail_sales
	WHERE total_sale > 1000;
```


**➡️Q6. Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**
```sql
SELECT
	category,
	gender,
	COUNT(*) AS total_trans
FROM retail_sales
GROUP 
	BY 
	category,
	gender
ORDER BY 1;
```


**➡️Q7. Write a SQL query to calculate the average sale for each month. Find out best selling month in each year.**
```sql
SELECT 
		year,
		month,
	avg_sale
FROM(
SELECT
	EXTRACT (YEAR FROM sale_date) AS year,
	EXTRACT (MONTH FROM sale_date) AS month,
	AVG(total_sale) AS avg_sale,	
	RANK() OVER (PARTITION BY EXTRACT (YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
FROM retail_sales
GROUP BY 1,2
) AS t1
WHERE rank = 1;
```


**➡️Q8. Write a SQL query to find the top 5 customers based on the highest total sales.**
```sql
SELECT 
	customer_id,
	SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```


**➡️Q9. Write a SQL query to find the number of unique customers who purchased items from each category.**
```sql
SELECT 
	category,
	COUNT(DISTINCT customer_id) AS unique_customer
FROM retail_sales
group by category;
```


**➡️Q10. Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17).**
```sql
WITH hourly_sales
AS
(
SELECT * ,
	CASE
		WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'MORNING'
		WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'AFTERNOON' 
		ELSE 'EVENING'
		END AS shifts
FROM retail_sales
)
SELECT 
	shifts,
	COUNT(*) AS total_sales
FROM hourly_sales
	GROUP BY shifts;
```

## 5. Business Recommendations (The "So What?" Section)
Data analysts don't just write queries; they solve business problems. Add a section explaining what a business should *do* with the information you found.

markdown
## 💡 Strategic Business Recommendations

Based on the insights derived from the SQL analysis, here are the core recommendations for the retail store management:

* **Shift Optimization (Q10):** Since sales spike significantly during specific shifts, management should align staffing schedules to ensure peak hours (e.g., Afternoon shifts) are fully staffed, while reducing overhead during slower Morning hours.
* **Targeted Marketing (Q4 & Q6):** With the average age of 'Beauty' product buyers calculated, marketing campaigns for this category should be precisely targeted toward that specific age demographic via social media channels.
* **Inventory Focus (Q3 & Q8):** Prioritize inventory management and restocking schedules for the top-performing categories and ensure high-value customers (Top 5) are enrolled in a premium loyalty program to increase retention.
  

---


##  6. Power BI Report Overview
<img width="1290" height="732" alt="Screenshot 2026-06-19 124109" src="https://github.com/user-attachments/assets/7bc4c3af-6dd3-4d42-896f-708047ff9207" />

<img width="1291" height="735" alt="Screenshot 2026-06-19 124256" src="https://github.com/user-attachments/assets/150cec32-89c1-4281-982d-f066c7cfed05" />


## 🔮7. Conclusion & Future Work
This project successfully demonstrates how raw transactional retail logs can be cleaned, structured, and transformed into rich operational intelligence using PostgreSQL. 

**Next Steps:**
 * **Dashboard Integration:** The next phase of this project involves connecting this PostgreSQL database to **Power BI** or **Tableau** to build an interactive sales performance dashboard.
 * **Predictive Analytics:** Utilizing Python to build a regression model forecasting next month's sales trends based on the historical data cleaned here.

## 👨‍💻 Author

**Name** **Tejas Jagtap**

Let's connect! Whether you have questions about this project, want to collaborate on a data initiative, or just want to talk shop about SQL, feel free to reach out:

**💼 LinkedIn:** https://www.linkedin.com/in/tejasjagtap01/
**🐙 GitHub Portfolio:** https://github.com/tejasjagtap-01

Thank you for checking out my project! 🚀
