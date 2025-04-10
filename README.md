# Using SQL to Pull Sales Data
With the use of MySQL Workbench, I looked at data from this Kaggle data set https://www.kaggle.com/datasets/poojacareer/sales-data-analysis-using-mysql-excel-and-power-bi?select=superstore_final_dataset.csv
and utilized different SQL queries to answer some sales data questions on the furniture, technology, and office equipment this company is selling. 

We have an Order table containing the company's order information, with each row being attributed to a product that was sold. We also have details such as the date the order was place, and customer and product information. The data spans 4 years from 2015-2018.
![image](https://github.com/user-attachments/assets/f049f936-584b-4528-b8bb-904236049429)

## Exploratory Sales Info Gathering
### What has been the total sales $ in each Region?

```sql
SELECT Region, FORMAT(SUM(sales),'C') as "Total Sales" FROM sales_project.superstore_sales_data
GROUP BY Region
ORDER BY sum(sales) DESC;
```
![image](https://github.com/user-attachments/assets/a2b115e1-45f5-43d2-82b9-7f480998a61f)

The region with the highest $ amount of sales (summed across all years) is the West

### Which Product Categories Drive the Highest Sales?

```sql
SELECT Category, FORMAT(SUM(sales),'C') as "Total Sales" FROM sales_project.superstore_sales_data
GROUP BY Category
ORDER BY SUM(sales) DESC;
```
![image](https://github.com/user-attachments/assets/e1686bc8-c0b5-4e1a-9f24-8685ba4d37a8)

This would be the technology category! It includes items such as phones, accessories, and machines

### Who are the Top 10 customers by Sale $?

```sql
SELECT
    Customer_ID, 
    Customer_Name, 
    COUNT(DISTINCT(Order_ID)) AS "Number of Orders", 
    FORMAT(SUM(sales),'C') as "Total Sales($)" 
FROM sales_project.superstore_sales_data
GROUP BY Customer_Name
ORDER BY SUM(sales) DESC
LIMIT 10;
```
![image](https://github.com/user-attachments/assets/dfb6c3be-7bdd-46e1-8891-e5a440cc3adf)

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

Looks like the company has been quite consistent throughout the years; fulfilling orders within 4 days. 

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



