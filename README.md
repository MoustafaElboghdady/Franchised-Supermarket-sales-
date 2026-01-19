# Franchised-Supermarket-sales-
The growth of supermarkets in most populated cities are increasing and market competitions are also high. The dataset is one of the historical sales of supermarket company which has recorded in 3 different branches for 3 months data. Predictive data analytics methods are easy to apply with this dataset.

Context

The growth of supermarkets in most populated cities are increasing and market competitions are also high. The dataset is one of the historical sales of supermarket company which has recorded in 3 different branches for 3 months data. Predictive data analytics methods are easy to apply with this dataset.

Attribute information

Invoice id: Computer generated sales slip invoice identification number.

Branch: Branch of supercenter (3 branches are available identified by A, B and C).

City: Location of supercenters.

Customer type: Type of customers, recorded by Members for customers using member card and Normal for without member card.

Gender: Gender type of customer.

Product line: General item categorization groups - Electronic accessories, Fashion accessories, Food and beverages, Health and beauty, Home and lifestyle, Sports and travel.

Unit price: Price of each product in $.

Quantity: Number of products purchased by customer.

Tax: 5% tax fee for customer buying.

Total: Total price including tax.

Date: Date of purchase (Record available from January 2019 to March 2019).

Time: Purchase time (10am to 9pm).

Payment: Payment used by customer for purchase (3 methods are available – Cash, Credit card and Ewallet).

COGS: Cost of goods sold.

Gross margin percentage: Gross margin percentage.

Gross income: Gross income.

Rating: Customer stratification rating on their overall shopping experience (On a scale of 1 to 10).

Purpose
This dataset can be used for predictive data analytics purpose.



--------Customer Section-------------------------------------------------------------
CREATE Table Customer_statistics (
    invoice_id VARCHAR (50),
    city TEXT,
    customer_type TEXT,
    gender TEXT,
    product_line VARCHAR,
    quantity int,
    total_bill DECIMAL,
    payment_method VARCHAR,
    rating DECIMAL
);

Copy Customer_statistics  
    FROM 'F:\STUDY\Data Analysis\projects\Sales of a Supermarket\Customers Data.csv'
    DELIMITER ',' csv HEADER

Select * 
FROM Customer_statistics
--------------Products Data-----------------------------------------------------------------

CREATE Table product_statistics(
    invoice_id VARCHAR (50),
    gender VARCHAR,
    product_lice VARCHAR,
    unit_price DECIMAL,
    quantity INT,
    total_bill DECIMAL,
    date DATE,
    gross_income DECIMAL,
    rating DECIMAL
);

copy product_statistics 
    FROM 'F:\STUDY\Data Analysis\projects\Sales of a Supermarket\Product Data.csv'
    DELIMITER ',' CSV HEADER

SELECT * FROM product_statistics;

-----------------Brunchs Data-----------------------------------------------------

CREATE TABLE branches_statistics(
    invoice_id VARCHAR(50),
    branch VARCHAR,
    city VARCHAR,
    product_line VARCHAR,
    quantity DECIMAL,
    total_bill DECIMAL,
    cogs DECIMAL,
    gross_income DECIMAL
);
Copy branches_statistics 
    from 'F:\STUDY\Data Analysis\projects\Sales of a Supermarket\Brunches Data.csv'
    DELIMITER ',' CSV HEADER
SELECT *
FROM branches_statistics;
-------------------------------------------------------------------------------------

---------------top sold products and most profit----------------------------
SELECT 
    DISTINCT(TRIM(product_line)),
    SUM(quantity) AS total_sold,
    ROUND(SUM(total_bill), 2) AS total_sales,
    ROUND(SUM(gross_income), 2) AS gross
FROM product_statistics
GROUP BY TRIM(product_line);

----------------------------------------------------------------------------
---------------most sold line for each city-------------------------------

SELECT 
    DISTINCT ON (branches_statistics.city)
    branches_statistics.city,
    product_statistics.product_line,
    ROUND(SUM(branches_statistics.total_bill), 2) AS total_bills,
    ROUND(SUM(product_statistics.gross_income), 2) AS total_gross_income,
    SUM(branches_statistics.quantity) as total_qty
FROM branches_statistics 
    LEFT JOIN product_statistics
    ON branches_statistics.product_line = product_statistics.product_line
GROUP BY branches_statistics.city, product_statistics.product_line,
branches_statistics.total_bill
ORDER BY branches_statistics.city,total_bills DESC;

-----------------------------------------------------------------------------
---------------Customer Behaviour "Gender"-----------------------------------

SELECT DISTINCT ON (customer_statistics.gender)
    customer_statistics.gender,
   customer_statistics.product_line,
    SUM(customer_statistics.total_bill) AS total_bills,
    SUM(product_statistics.gross_income) AS total_gross_income
FROM customer_statistics 
    LEFT JOIN product_statistics
    ON customer_statistics.product_line = product_statistics.product_line

GROUP BY customer_statistics.gender,
    customer_statistics.product_line
ORDER BY customer_statistics.gender,
        total_gross_income DESC

----------------------------------------------------------------------------
----------------customer Behaviour "typt"-----------------------------------

SELECT DISTINCT ON (customer_statistics.customer_type)
    customer_statistics.customer_type,
       Round(SUM(product_statistics.total_bill), 2) AS totall_bills,
    ROUND(sum(product_statistics.gross_income), 2) as totall_gross
FROM customer_statistics 
    LEFT JOIN product_statistics
    ON customer_statistics.product_line = product_statistics.product_line
GROUP BY customer_statistics.customer_type

ORDER BY customer_statistics.customer_type

----------------------------------------------------------------------------
----------------city Operation--------------------------------------------
    ----Top Revenue city-----

SELECT DISTINCT ON(city)
    city,
    SUM(quantity) as count,
    Round(SUM(total_bill) ,2)as total_bills,
    ROUND(SUM(gross_income), 2) as gross
FROM branches_statistics
GROUP BY city
    
ORDER BY city,total_bills DESC;

    ----Peak months-----

SELECT DISTINCT ON(branches_statistics.city)
    TO_CHAR(product_statistics.date, 'Month') as month,
    branches_statistics.city,
    ROUND(SUM(branches_statistics.total_bill), 2)AS total_sales,
    COUNT(branches_statistics.city)AS total_visits
FROM branches_statistics 
LEFT JOIN product_statistics ON
branches_statistics.invoice_id = product_statistics.invoice_id
GROUP BY 
    branches_statistics.city,month
ORDER BY
    branches_statistics.city, total_sales DESC;

    ----most visited city------

SELECT 
    city,
    ROUND(SUM(total_bill), 2)AS sales,
    COUNT(invoice_id)AS total_visits
FROM branches_statistics
GROUP BY city
ORDER BY total_visits DESC;




