# Pizza Sales Analysis

### Project Overview
This analysis aims to provide insights about a Pizza Sales joint for a period of one year. By analyzing various aspects we seek to indentify trends, make data driven recommedations and gain deeper understanding of pizza sales joint for the period of one year.

### Data Sources
Sales Data: The primary data source used for this analysis is "pizza_sales.csv" file containing each sale record of the pizza sales joint

### Tools
- MS SQL Server- Data Analysis [Download Here](https://www.microsoft.com)
- Tableau Public- Data Visualization [Download Here](https://www.tableau.com/products/desktop/download)

### Data Cleaning and Preparation
In this phase I performed the following tasks:
 1. Data loading and Inspection
 2. Data Calculations- Using Tableau calculated fields and also creating aliases.


### Exploratory Data Analysis
EDA involved exploring the pizza sales data to answer key questions as:
1. How many customers do we have each day? Are there any peak hours?
2. How many pizzas are typically in an orders? Do we have any bestsellors?
3. How much revenue did we make this year? Can we indentify any seasonality in orders made?
4. Are there any pizzas we should take of the menu or any promotions we could leverage?


### Data Analysis
```SQL
-- Total Revenue--
SELECT ROUND(SUM(total_price), 2) AS Total_Revenue
FROM pizza_sales
```
```SQL
-- Average Pizzas Per Order--
SELECT CAST(CAST(SUM(quantity) AS DECIMAL(10,2))/ 
	   CAST(COUNT(DISTINCT order_id) AS DECIMAL(10, 2)) AS DECIMAL(10, 2)) AS Avg_Pizza_Per_Order
FROM pizza_sales
```
```SQL
--Hourly Trend for Pizza Sold

SELECT DATEPART(HOUR, order_time) AS Order_Hour, 
	   SUM(quantity) AS Total_Pizza_Sold 
FROM pizza_sales
GROUP BY DATEPART(HOUR, order_time)
ORDER BY DATEPART(HOUR, order_time)
```
```SQL
--Percentage of Sales by Pizza Size
SELECT 
	a.pizza_size_name, 
	a.percentage_size_sales 
FROM
	(SELECT CASE
			WHEN pizza_size= 'S' THEN 'Small'
			WHEN pizza_size= 'M' THEN 'Medium'
			WHEN pizza_size= 'L' THEN 'Large'
			WHEN pizza_size= 'XL' THEN 'X-Large'
			WHEN pizza_size= 'XXL' THEN 'XX-Large'
			ELSE 'Invalid'
		END AS 'pizza_size_name',
		pizza_size,
		CAST(SUM(total_price) AS DECIMAL(10,2)) pizza_size_sales,
		CONCAT(CAST((SUM(total_price) / SUM(SUM(total_price)) OVER() * 100) AS DECIMAL(10,2)), '%') percentage_size_sales
	FROM pizza_sales
	GROUP BY pizza_size) a
ORDER BY a.percentage_size_sales DESC
```
```SQL
--Top 5 Pizzas- Revenue

SELECT TOP 5
	pizza_name, 
	CAST(SUM(total_price) AS DECIMAL(10,2)) total_revenue 
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue DESC
```
```SQL
--Bottom 5 Pizzas- Revenue
SELECT TOP 5
	pizza_name, 
	CAST(SUM(total_price) AS DECIMAL(10,2))total_revenue 
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_revenue ASC
```
```SQL
--Top 5 Pizzas- Orders
SELECT TOP 5 
	pizza_name, 
	COUNT(order_id) total_orders
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_orders DESC
```
```SQL
--Bottom 5 Pizzas- Orders
SELECT TOP 5 
	pizza_name, 
	COUNT(order_id) total_orders
FROM pizza_sales
GROUP BY pizza_name
ORDER BY total_orders ASC
```
```SQL
--Daily Trend of Total Orders
SELECT 
	a.day_name, 
	a.total_orders 
FROM
	(SELECT 
		DATEPART(WEEKDAY, order_date) day_number,
		DATENAME(WEEKDAY, order_date) day_name,
		COUNT(DISTINCT order_id) total_orders
	FROM pizza_sales
	GROUP BY DATEPART(WEEKDAY, order_date), DATENAME(WEEKDAY, order_date)) a
ORDER BY a.day_number
```

### Recommedations
Based on analysis, we recommend the following action:
- Invest in marketing and brand promotions for the worst performing pizzas during the peak hours to increase total orders made
- Offer free bites of the worst performing pizzas to customers to create awareness
- Run a poll on any improvements based on the free bites offered to the worst performing pizzas


### Results and Findings
The analysis results are summarized as follows:
- Peak hours is between 12 noon and 1 p.m. and in the evening between 5 p.m. and 7 p.m.
- Highest peak is the 48 week in the month of Dec.
- Classic Category contributes maximum revenue, total orders and total pizza sold.
- Large pizza size contributes maximum revenue.
- The Thai Chicken Pizza contributes maximum revenue.
- The Classic Deluxe Pizza has the highest number of orders and pizza sold.
- Brie Carre Pizza contributes least in revenue, total orders and total pizzas sold.


### References
- [SQL W3Schools](https://www.w3schools.com/sql/default.asp)
- [Stack Overflow](https://stackoverflow.com/)
- [Tableau Calculations](https://help.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_create.htm)
