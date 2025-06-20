# Library_Management  
**Project Title: Library Management System**   
**Database: library_project2**  
This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.  

# Objectives
1.**Set up the Library Management System Database:** Create and populate the database with tables for branches, employees, members, books, issued status, and return status.  
2.**CRUD Operations:** Perform Create, Read, Update, and Delete operations on the data.  
3.**CTAS (Create Table As Select):** Utilize CTAS to create new tables based on query results.  
4.**Advanced SQL Queries:** Develop complex queries to analyze and retrieve specific data.  
# Project Structure
**DataBase setup**    

![ER_Diagram_Lib](https://github.com/user-attachments/assets/3fb0454f-fd2b-44d9-bc6d-05966ee1afd9)

**Database Creation:** Created a database named library_project2.    
**Table Creation:** Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.    
``` SQL
CREATE TABLE library_project2
DROP TABLE IF EXISTS branch;
CREATE TABLE branch
(
            branch_id VARCHAR(10) PRIMARY KEY,
            manager_id VARCHAR(10),
            branch_address VARCHAR(30),
            contact_no VARCHAR(15)
);


-- Create table "Employee"
DROP TABLE IF EXISTS employees;
CREATE TABLE employees
(
            emp_id VARCHAR(10) PRIMARY KEY,
            emp_name VARCHAR(30),
            position VARCHAR(30),
            salary DECIMAL(10,2),
            branch_id VARCHAR(10),
            FOREIGN KEY (branch_id) REFERENCES  branch(branch_id)
);


-- Create table "Members"
DROP TABLE IF EXISTS members;
CREATE TABLE members
(
            member_id VARCHAR(10) PRIMARY KEY,
            member_name VARCHAR(30),
            member_address VARCHAR(30),
            reg_date DATE
);



-- Create table "Books"
DROP TABLE IF EXISTS books;
CREATE TABLE books
(
            isbn VARCHAR(50) PRIMARY KEY,
            book_title VARCHAR(80),
            category VARCHAR(30),
            rental_price DECIMAL(10,2),
            status VARCHAR(10),
            author VARCHAR(30),
            publisher VARCHAR(30)
);



-- Create table "IssueStatus"
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status
(
            issued_id VARCHAR(10) PRIMARY KEY,
            issued_member_id VARCHAR(30),
            issued_book_name VARCHAR(80),
            issued_date DATE,
            issued_book_isbn VARCHAR(50),
            issued_emp_id VARCHAR(10),
            FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
            FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
            FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn) 
);



-- Create table "ReturnStatus"
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status
(
            return_id VARCHAR(10) PRIMARY KEY,
            issued_id VARCHAR(30),
            return_book_name VARCHAR(80),
            return_date DATE,
            return_book_isbn VARCHAR(50),
            FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
);  
```
2. **CRUD Operations**  
**Create:** Inserted sample records into the books table.  
**Read:** Retrieved and displayed data from various tables.  
**Update:** Updated records in the employees table.  
**Delete:** Removed records from the members table as needed.  
Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```
**Task 2:** Update an Existing Member's Address  

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```
**Task 3:** Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.  
```sql
DELETE FROM issued_status
WHERE   issued_id =   'IS121';
``` 
**Task 4:** Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'.  

```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101';
```
**Task 5:** List Members Who Have Issued More Than One Book -- Objective:  Find members who have issued more than one book. 
```sql
SELECT issued_emp_id,
count(*),
FROM issued_status
GROUP BY 1
Having count(*)>1;
```
3. **CTAS (Create Table As Select)**    
   **Task 6:** Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt  
```sql
CREATE TABLE book_issued_cnt AS
SELECT
b.isbn,
b.b.book_title,
COUNT(ist.issued_id) AS issued_cnt
FROM books AS b
join issued_status AS ist on
ist.issued_book_isbn = bk.isbn
GROUP BY b.isbn,b.b.book_title;
```
**4. Data Analysis & Findings**  
**Task 7.** Retrieve All Books in a Specific Category:  
```sql
SELECT * FROM books
WHERE category = 'classic';
```
**Task 8:** Find Total Rental Income by Category:  
```sql
SELECT 
b.category,
SUM(b.rental_price) AS total_income,
COUNT(*)
FROM books AS b
JOIN issued_status AS ist on 
ist.issued_book_isbn = b.isbn
GROUP BY category;
```
**Task 9:** List Members Who Registered in the Last 180 Days:  
```sql
SELECT * FROM members
WHERE reg_date >= DATE_SUB(CURDATE(), INTERVAL 180 DAY);
```
**Task 10.** List Employees with Their Branch Manager's Name and their branch details:  
```sql
SELECT
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id
```
**Task 11.** Create a Table of Books with Rental Price Above a Certain Threshold:   
```sql
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;
```
**Task 12:**  Retrieve the List of Books Not Yet Returned  
```sql
SELECT * FROM issued_status as ist
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;
```

