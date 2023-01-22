# azuredatafactory

Joins in SQL
=============
-- Create the orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10, 2)
);

-- Create the customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(50),
    address VARCHAR(100),
    phone VARCHAR(20)
);

-- Insert sample data into the orders table
INSERT INTO orders (order_id, customer_id, order_date, total_amount)
VALUES (1, 1, '2022-01-01', 100),
       (2, 2, '2022-01-02', 200),
       (3, 3, '2022-01-03', 300);

-- Insert sample data into the customers table
INSERT INTO customers (customer_id, name, address, phone)
VALUES (1, 'John Doe', '123 Main St', '555-555-5555'),

INNER JOIN: This type of join returns only the rows that have matching values in both tables. It is the most common type of join and is the default when using the JOIN keyword.
===========
SELECT * 
FROM orders 
JOIN customers ON orders.customer_id = customers.customer_id

LEFT JOIN: This type of join returns all rows from the left table (table1), and the matching rows from the right table (table2). If there is no match, NULL values will be returned for right table's columns.
===========
SELECT * 
FROM orders 
LEFT JOIN customers ON orders.customer_id = customers.customer_id

RIGHT JOIN: This type of join returns all rows from the right table (table2), and the matching rows from the left table (table1). If there is no match, NULL values will be returned for left table's columns.
===========
SELECT * 
FROM orders 
RIGHT JOIN customers ON orders.customer_id = customers.customer_id

FULL OUTER JOIN: This type of join returns all rows from both tables, matching the rows from both tables and returning NULL values for non-matching rows.
===========
SELECT * 
FROM orders 
FULL OUTER JOIN customers ON orders.customer_id = customers.customer_id


SCD Type1 With Stored Procedure:
===============================

-- Create the source table
CREATE TABLE source_customer (
    customer_id INT PRIMARY KEY,
    name VARCHAR(50),
    address VARCHAR(100),
    phone VARCHAR(20)
);

-- Create the dimension table
CREATE TABLE dim_customer (
    customer_key INT PRIMARY KEY IDENTITY(1,1),
    customer_id INT,
    name VARCHAR(50),
    address VARCHAR(100),
    phone VARCHAR(20)
);

-- Create stored procedure to update the dimension table
CREATE PROCEDURE update_customer
    @customer_id INT,
    @name VARCHAR(50),
    @address VARCHAR(100),
    @phone VARCHAR(20)
AS
BEGIN
    -- First, check if the customer already exists in the dimension table
    IF EXISTS (SELECT 1 FROM dim_customer WHERE customer_id = @customer_id)
    BEGIN
        -- If the customer already exists, update the existing record
        UPDATE dim_customer
        SET name = @name,
            address = @address,
            phone = @phone
        WHERE customer_id = @customer_id
    END
    ELSE
    BEGIN
        -- If the customer does not exist, insert a new record
        INSERT INTO dim_customer (customer_id, name, address, phone)
        VALUES (@customer_id, @name, @address, @phone)
    END
END

-- Insert sample data into the source table
INSERT INTO source_customer (customer_id, name, address, phone)
VALUES (1, 'John Doe', '123 Main St', '555-555-5555')

-- Run the stored procedure
EXEC update_customer 1, 'John Doe', '456 Park Ave', '555-555-5555'

-- Check the dimension table
SELECT * FROM dim_customer
