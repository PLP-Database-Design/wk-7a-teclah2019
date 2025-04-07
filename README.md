# 📝 Assignment: Database Design and Normalization

## 🎯 **Learning Objectives**
* 🛠️ **Understand the principles of good database design** and **normalization**.
* 💡 **Apply normalization techniques** to improve database structure and efficiency.
* 🔍 **Learn First, Second, and Third Normal Forms** (1NF, 2NF, 3NF) to eliminate redundancy and optimize data storage.

---

## 📋 **What You'll Need**
* 💻 A computer with internet access.
* ✍️ A code editor (e.g., Visual Studio Code).
* 🖥️ MySQL Workbench or another SQL database environment.

---


## 📝 Submission Instructions  
📂 Write all your SQL queries in the **answers.sql** file.  
✍️ Answer each question concisely and make sure your queries are clear and correct.  
🗣️ Structure your responses clearly, and use comments if necessary to explain your approach.

--- 

## 📚 Assignment Questions

---

### Question 1 Achieving 1NF (First Normal Form) 🛠️
Task:
- You are given the following table **ProductDetail**:

| OrderID | CustomerName  | Products                        |
|---------|---------------|---------------------------------|
| 101     | John Doe      | Laptop, Mouse                   |
| 102     | Jane Smith    | Tablet, Keyboard, Mouse         |
| 103     | Emily Clark   | Phone                           |


- In the table above, the **Products column** contains multiple values, which violates **1NF**.
- **Write an SQL query** to transform this table into **1NF**, ensuring that each row represents a single product for an order
To transform the ProductDetail table into 1NF, we need to eliminate the multi-valued column (Products). Each product should have its own row. Here's how you can achieve this:

Original Table: ProductDetail
OrderID	CustomerName	Products
101	John Doe	Laptop, Mouse
102	Jane Smith	Tablet, Keyboard, Mouse
103	Emily Clark	Phone


-- Create a new table for 1NF transformation
CREATE TABLE ProductDetail_1NF (
    OrderID INT,
    CustomerName VARCHAR(255),
    Product VARCHAR(255)
);

-- Insert data into the new table, splitting the Products column
INSERT INTO ProductDetail_1NF (OrderID, CustomerName, Product)
VALUES
(101, 'John Doe', 'Laptop'),
(101, 'John Doe', 'Mouse'),
(102, 'Jane Smith', 'Tablet'),
(102, 'Jane Smith', 'Keyboard'),
(102, 'Jane Smith', 'Mouse'),
(103, 'Emily Clark', 'Phone');




Resulting Table: ProductDetail_1NF
OrderID	CustomerName	Product
101	John Doe	Laptop
101	John Doe	Mouse
102	Jane Smith	Tablet
102	Jane Smith	Keyboard
102	Jane Smith	Mouse
103	Emily Clark	Phone
--- 

### Question 2 Achieving 2NF (Second Normal Form) 🧩

- You are given the following table **OrderDetails**, which is already in **1NF** but still contains partial dependencies:

| OrderID | CustomerName  | Product      | Quantity |
|---------|---------------|--------------|----------|
| 101     | John Doe      | Laptop       | 2        |
| 101     | John Doe      | Mouse        | 1        |
| 102     | Jane Smith    | Tablet       | 3        |
| 102     | Jane Smith    | Keyboard     | 1        |
| 102     | Jane Smith    | Mouse        | 2        |
| 103     | Emily Clark   | Phone        | 1        |

- In the table above, the **CustomerName** column depends on **OrderID** (a partial dependency), which violates **2NF**. 

- Write an SQL query to transform this table into **2NF** by removing partial dependencies. Ensure that each non-key column fully depends on the entire primary key.
To achieve 2NF, we need to remove partial dependencies. In the OrderDetails table, CustomerName depends on OrderID, which is a partial dependency. To resolve this, we need to split the table into two tables: one for order information and another for customer details.

Original Table: OrderDetails
OrderID	CustomerName	Product	Quantity
101	John Doe	Laptop	2
101	John Doe	Mouse	1
102	Jane Smith	Tablet	3
102	Jane Smith	Keyboard	1
102	Jane Smith	Mouse	2
103	Emily Clark	Phone	1


SQL Query to Achieve 2NF:
Create the Customers table (removes partial dependency):

-- Create Customers table
CREATE TABLE Customers (
    CustomerID INT AUTO_INCREMENT PRIMARY KEY,
    CustomerName VARCHAR(255)
);

-- Insert customer data into the Customers table
INSERT INTO Customers (CustomerName)
SELECT DISTINCT CustomerName
FROM OrderDetails;


-- Create Orders table
CREATE TABLE Orders (
    OrderID INT,
    CustomerID INT,
    Product VARCHAR(255),
    Quantity INT,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

-- Insert order details into the Orders table
INSERT INTO Orders (OrderID, CustomerID, Product, Quantity)
SELECT OrderID, 
       (SELECT CustomerID FROM Customers WHERE CustomerName = OrderDetails.CustomerName), 
       Product, 
       Quantity
FROM OrderDetails;



Resulting Tables:
Customers Table:

CustomerID	CustomerName
1	John Doe
2	Jane Smith
3	Emily Clark
Orders Table:

OrderID	CustomerID	Product	Quantity
101	1	Laptop	2
101	1	Mouse	1
102	2	Tablet	3
102	2	Keyboard	1
102	2	Mouse	2
103	3	Phone	1
By splitting the tables into Customers and Orders, we have removed the partial dependency (CustomerName now depends on the CustomerID, not OrderID), thus transforming the table into 2NF.
---
Good luck 🚀
