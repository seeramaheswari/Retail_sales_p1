# Retail_sales_p1
Retail Sales Analysis using SQL: Analyzed a retail sales database using SQL to perform data cleaning, exploration, and advanced analysis with aggregations, CTEs, window functions, and date functions. Generated insights on sales trends, customer behavior, category performance, best-selling periods, and shift-wise order distribution.


**Creating Retail sales table**
This SQL code selects the retail_sales_database and creates a retail_sales table to store retail transaction data. The table includes details such as transaction ID, sale date and time, customer information, product category, quantity sold, pricing, cost of goods sold (COGS), and total sales amount. The final query retrieves all records from the table for verification and analysis.
create table retail_sales 
( transactions_id int primary key, sale_date date, 
sale_time time, customer_id int, gender varchar(10), 
age int, category varchar(35), quantity int, price_per_unit float, 
cogs float, total_sale float ); 
select * from retail_sales;
**To find null values in each column**


select * from retail_sales where transactions_id is null;
select * from retail_sales where transactions_id is null or 
sale_time is null
or sale_date is null
or gender is null
or category is null
or quantity is null
or cogs is null
or total_sale is null;
**Data exploration**
This set of SQL queries performs basic data exploration on the retail_sales table. It calculates the total number of sales records, identifies the number of unique customers and product categories, and retrieves distinct customer IDs and category names. These queries help in understanding the overall size, diversity, and structure of the retail sales dataset before performing deeper analysis.

-- How many sales we have?
select count(*) as total_sales from retail_sales;
-- How many unique customers we have?
select count(distinct customer_id) as total_sales from retail_sales;
-- How many unique category we have?
select count(distinct category) as total_sales from retail_sales;
-- Retrieve unique customer ids in table
select distinct customer_id from retail_sales;
-- Retrieve unique category names in table
select distinct category from retail_sales;


**Data Analysis & 12 Buisiness key problems and answers**
--**Analysis and findings**

-- **Q.1 write sql query to retrieve all sales made on '2022-11-05'**

select * from retail_sales where sale_date='2022-11-05';

-- **Q.2 write sql query to retrieve all transactions where category is clothing  and the quantity sold is more than 3 in the month of nov-2022**

select from retail_sales where category='Clothing'
and sale_date>='2022-11-1'
and sale_date<='2022-11-30' 
and quantity>3;

-- **Q.3 write a sql query  to calculate the total sales (total_sales) for each category**

select category,sum(total_sale) as net_sale,count(*) as total_order from retail_sales group by category;

-- **Q.4 wriite sql query to find average age of customers who purchased items from the 'beauty' category**

select round(avg(age),2) as avg_age from retail_sales where category='beauty';

-- **Q.5 write a sql query  to find all transactions where the total sales is greater than 1000**

select * from retail_sales where total_sale>1000;

--**Q.6 write a sql query  to find total number of transactions (transactions_id) made by each gender in each category**

select category,gender, count(*) as total_transactions from retail_sales group by category,gender order by 1;

-- **Q.7 write a sql query  to calculate the average sale of each month find out best selling month in each year.**

select extract(YEAR from sale_date) as year,
extract(MONTH from sale_date) as month,
round(avg(total_sale),2) as avg_sale from retail_sales group by year,month order by 1,3 desc;

-- **Q.8 write a query to find best selling month in Each year?**

select * from
(
select  year,month , avg_sale, 
rank() over(partition by year order by avg_sale desc) as top
from (
select 
extract(year from sale_date) as year,
extract(month from sale_date) as month , 
round(avg(total_sale),2) as avg_sale from 
retail_sales group by year, month
) t1
) t2
where top=1;

-- **Q.9 write sql query  to find the top 5` customers based on the highest total sale** 

select customer_id ,sum(total_sale) as total_sales from retail_sales
group by 1 order by 2 desc limit 5;

--**Q.10 write sql query to find number of unique customers from each category .**

select category,count(distinct customer_id) as total_customers 
from retail_sales group by category;

--**Q.10 write sql query to create each shifts and number of orders (example morning <=12,afternoon between 12 & 17,evening >17)**

with hourly_sale
as
(
select *,
case 
when extract(hour from sale_time) <12 then 'morning'
when extract(hour from sale_time) between 12 and 17 then 'afternoon'
else 'evening'
end as shift
from retail_sales
)
select shift,count(*) as total_orders from hourly_sale group by 1;

--**Q. 11  write sql query to find total count of cogs for each transaction_id**

select transactions_id,round((cogs),1) from retail_sales group by 1 order by 1 desc limit 10;

--**Q.12 write sql query to find which count of cogs in each month**

select * from
(
select  year,month , count_cogs, 
rank() over(partition by year order by count_cogs desc) as c1
from (
select 
extract(year from sale_date) as year,
extract(month from sale_date) as month , 
round(sum(cogs),2) as count_cogs from 
retail_sales group by year, month
) t4
) t5
-- where c1=1;

**End of project**


