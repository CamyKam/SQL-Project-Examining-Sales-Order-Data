# Using SQL to Pull & Examine Sales Data
With the use of MySQL Workbench, I looked at data from this Kaggle data set https://www.kaggle.com/datasets/poojacareer/sales-data-analysis-using-mysql-excel-and-power-bi?select=superstore_final_dataset.csv
and utilized different SQL queries to answer some sales data questions on the furniture, technology, and office equipment this company is selling. 

We have an Order table containing the company's order information, with each row being attributed to a product that was sold. We also have details such as the date the order was place, and customer and product information. The data spans 4 years from 2015-2018.
![image](https://github.com/user-attachments/assets/f049f936-584b-4528-b8bb-904236049429)

## Exploratory Sales Info Gathering
### What has been the total sales $ in each Region?

```sql
SELECT Region, CONCAT('$',FORMAT(SUM(Sales),2)) as "Total Sales"
FROM sales_project.superstore_sales_data
GROUP BY Region
ORDER BY sum(sales) DESC;
```
![image](https://github.com/user-attachments/assets/983ea5c9-fce0-40b6-a6eb-1cefa8e53c6c)

The region with the highest $ amount of sales (summed across all years) is the West

### Which Product Categories Drive the Highest Sales?

```sql
SELECT Category, CONCAT('$',FORMAT(SUM(Sales),2)) as "Total Sales"
FROM sales_project.superstore_sales_data
GROUP BY Category
ORDER BY sum(sales) DESC;
```
![image](https://github.com/user-attachments/assets/7df76016-8bdb-4fc1-aa7d-e1cb8a94ea2f)

This would be the technology category! It includes items such as phones, accessories, and machines

### Who are the Top 10 customers by Sale $?

```sql
SELECT
    Customer_ID, 
    Customer_Name, 
    COUNT(DISTINCT(Order_ID)) AS "Number of Orders", 
    CONCAT('$',FORMAT(SUM(Sales),2)) as "Total Sales($)" 
FROM sales_project.superstore_sales_data
GROUP BY Customer_Name
ORDER BY SUM(sales) DESC
LIMIT 10;
```
![image](https://github.com/user-attachments/assets/a8e88798-0007-487b-8006-6f92248dc5fb)

Mr.Sean Miller takes 1st place here, with total sales $ of his orders amounting to $24,196. That's 76.4% higher than the second top customer! If the company ever wanted to implement a VIP program they might want to consider reaching out to this inidividual first.

## Shipping and Fulfillment Information
### What Is the Average Number of Days It Takes to Fullfill an Order (By Year)?

```sql
SELECT YEAR(Order_Date) AS 'Year', AVG(datediff(Ship_Date, Order_Date)) AS 'Average # of Days to Ship' 
FROM sales_project.superstore_sales_data
GROUP BY Year
ORDER BY Year ASC;
```
![image](https://github.com/user-attachments/assets/f3c067a8-b4a3-4105-b7aa-edb3ee6cb9c5)

Looks like the company has been quite consistent throughout the years; fulfilling orders within 4 days. Let's take an additional look at fulfilment/shipping time.

```sql
SELECT Region, AVG(datediff(Ship_Date, Order_Date)) AS 'Average # of Days to Ship' 
FROM sales_project.superstore_sales_data
GROUP BY Region;
```
```sql
SELECT Category, AVG(datediff(Ship_Date, Order_Date)) AS 'Average # of Days to Ship' 
FROM sales_project.superstore_sales_data
GROUP BY Category;
```
![image](https://github.com/user-attachments/assets/6fa13b25-235f-4df6-9e4d-a799c0aa3057)  ![image](https://github.com/user-attachments/assets/ddcd965d-2f3e-46f2-b25e-9d45a1867486)

The average number of days to fulfill and ship an order is approximately the same regardless of destination region, with the Central region orders being only slightly higher. 
I also wanted to see if the category of the order items being shipped out affected the fulfillment time but overall, there was very minimal differences. 

### Senior Management is Considering Changing the Shipping Structure into 2 categories: Priority and Standard base on the Ship Mode. If that's the case, how many historical orders would be considered 'Priority', and how many would be 'Standard'? 
We assume that each item in an order gets shipped out in one 'package' thus being considered one order

```SQL
SELECT
CASE 
  WHEN Ship_Mode='Same Day' THEN 'priority shipping'
  WHEN Ship_Mode='First Class' THEN 'priority shipping'
  WHEN Ship_Mode='Second Class' THEN 'standard shipping'
  WHEN Ship_Mode='Standard Class' THEN 'standard shipping'
  ELSE NULL
END AS shipment_types,
COUNT(DISTINCT(Order_ID)) AS 'Number of Orders'
FROM sales_project.superstore_sales_data
GROUP BY shipment_types;
```
![image](https://github.com/user-attachments/assets/715afc14-9651-4871-b64d-0f21722fc1f2)

If we were to adopt this change in shipping structure, we can expect about 78.6% of our orders to be sent out as Standard Shipping

## Average Orders/Sales per Month

```sql
SELECT 
    Month, CEILING(AVG(Number_of_Orders)) AS 'Average Number of Orders',  
    CONCAT('$',FORMAT(AVG(total_sales_$),2)) AS 'Average Sales(in $)'
FROM(
    SELECT YEAR(Order_Date) AS 'Year', 
        MONTH(Order_Date) AS 'Month',
        COUNT(distinct(Order_ID)) AS Number_of_Orders,
        SUM(Sales) AS total_sales_$ 
    FROM sales_project.superstore_sales_data
    GROUP BY Year, Month
)AS distinct_orders_by_month
Group BY Month
Order BY Month ASC;
```
![image](https://github.com/user-attachments/assets/548caa73-c037-46a0-814f-870bee6cd9bf)

The query above provides me a general sense of the average number of orders and sales to expect for each month (based on 4 years worth of data). I used CEILING() to round up the order averages to whole integer values. Looking at the results, we can expect the number of orders to peak around Sept, Nov, Dec while Jan, Feb are anticipated to have the lowest number of orders. I hypothesize that the peak months are due to the back-to-school season along with the holidays, but I do not have enough additional details or context in this project to further prove that hypothesis.




