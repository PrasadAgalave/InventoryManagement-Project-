# InventoryManagement-Project-

1. Project Overview
The Inventory Management System is designed to track products, suppliers, and transactions in an inventory system. This system is intended for businesses that need to monitor product stock, manage orders, track sales and purchases, and maintain supplier information. The project uses SQL for the database and demonstrates key features like handling inventory, generating reports, and optimizing database performance.



-- Create a Database for InventoryManagement 
 
CREATE DATABASE InventoryManagement;
USE InventoryManagement;

-- Create Suppliers Table

CREATE TABLE Suppliers (
    Supplier_ID INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(255),
    Contact_Info VARCHAR(255)
);

-- Create Categories Table

CREATE TABLE Categories (
    Category_ID INT AUTO_INCREMENT PRIMARY KEY,
    Category_Name VARCHAR(255)
);

-- Create Products Table

CREATE TABLE Products (
    Product_ID INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(255),
    Category_ID INT,
    Price DECIMAL(10, 2),
    Stock_Quantity INT,
    Supplier_ID INT,
    FOREIGN KEY (Category_ID) REFERENCES Categories(Category_ID), 
    FOREIGN KEY (Supplier_ID) REFERENCES Suppliers(Supplier_ID)
);

-- Create orders Table

CREATE TABLE Orders (
    Order_ID INT AUTO_INCREMENT PRIMARY KEY,
    Supplier_ID INT,
    Product_ID INT,
    Order_Date DATE,
    Quantity_Ordered INT,
    Status VARCHAR(50),
    FOREIGN KEY (Supplier_ID)
        REFERENCES Suppliers (Supplier_ID),
    FOREIGN KEY (Product_ID)
        REFERENCES Products (Product_ID)
);


-- Create Transactions Table

CREATE TABLE Transactions (
    Transaction_ID INT AUTO_INCREMENT PRIMARY KEY,
    Product_ID INT,
    Transaction_Type VARCHAR(50),
    Quantity INT,
    Date DATE,
    FOREIGN KEY (Product_ID)
        REFERENCES Products (Product_ID)
);


-- INSERTING SAMPLE DATA FOR TABLES

INSERT INTO Suppliers (Name, Contact_Info)
VALUES 
('Ajit Supplies', '123-456-7890'),
('Shym Traders', '987-654-3210');

INSERT INTO Categories (Category_Name)
VALUES 
('Electronics'),
('Furniture'),
('Clothing'),
('Books');

INSERT INTO Products (Name, Category_ID, Price, Stock_Quantity, Supplier_ID)
VALUES 
('Laptop', 1, 50000, 10, 1), 
('Smartphone', 1, 30000, 25, 2),  
('Sofa', 2, 15000, 5, 1), 
('T-shirt', 3, 500, 50, 2),  
('Book on SQL', 4, 200, 100, 1);  

INSERT INTO Orders (Supplier_ID, Product_ID, Order_Date, Quantity_Ordered, Status)
VALUES 
(1, 1, '2024-12-01', 5, 'Shipped'),
(2, 2, '2024-12-05', 10, 'Pending');

INSERT INTO Transactions (Product_ID, Transaction_Type, Quantity, Date)
VALUES (1, 'Sale', 1, '2024-12-31');



-- 1. Query to View All Products and Their Stock Levels

SELECT p.Product_ID, p.Name, c.Category_Name, p.Price, p.Stock_Quantity
FROM Products p
JOIN Categories c ON p.Category_ID = c.Category_ID;

-- 2. Generate a Sales Report (Total Quantity)

SELECT p.Name, SUM(t.Quantity) AS Total_Sold
FROM Transactions t
JOIN Products p ON t.Product_ID = p.Product_ID
WHERE t.Transaction_Type = 'Sale'
GROUP BY p.Product_ID;

-- 3. Update Stock After Sale

UPDATE Products
SET Stock_Quantity = Stock_Quantity - 1
WHERE Product_ID = 1;


-- Trigger to Automatically Update Stock After Sale

DELIMITER //
CREATE TRIGGER UpdateStockAfterSale
AFTER INSERT ON Transactions
FOR EACH ROW
BEGIN
    IF NEW.Transaction_Type = 'Sale' THEN
        UPDATE Products
        SET Stock_Quantity = Stock_Quantity - NEW.Quantity
        WHERE Product_ID = NEW.Product_ID;
    END IF;
END //
DELIMITER ;

-- Stored Procedure to Add a New Product

DELIMITER //
CREATE PROCEDURE AddNewProduct(
    IN productName VARCHAR(255),
    IN productCategory INT,   -- Category ID (Foreign Key)
    IN productPrice DECIMAL(10,2),
    IN stockQuantity INT,
    IN supplierID INT
)
BEGIN
    INSERT INTO Products (Name, Category_ID, Price, Stock_Quantity, Supplier_ID)
    VALUES (productName, productCategory, productPrice, stockQuantity, supplierID);
END //
DELIMITER ;

-- Call the stored procedure and add a new product
CALL AddNewProduct('Smartwatch', 1, 15000, 100, 2);


-- Add Indexes on Frequently Used Columns

CREATE INDEX idx_product_id ON Products (Product_ID);
CREATE INDEX idx_supplier_id ON Suppliers (Supplier_ID);


-- Analyzing and Optimizing Queries

EXPLAIN SELECT p.Name, c.Category_Name, p.Price, p.Stock_Quantity
FROM Products p
JOIN Categories c ON p.Category_ID = c.Category_ID;





