- ### Referential integrity
    - refers to <strong>accuracy</strong> and consistency of data within a relationship
    - in relationships,data is linked between two or more tables
    - achieved by having the foreign key (in the associated table) reference a primary key value(in parent table)
- ### in the new user we created we are going to create the next commands
    - create table employee and department where employee's dept_id refers to department tables dept_id.
    ```
    create table department(
dept_id number primary key,
dept_name varchar2(20));
```
```
insert into department values(1,'IT');
insert into department values(2,'HR');
insert into department values(3,'MEDICAL');
insert into department values(4,'QUALITY');
```
- on delete cascade will delete the rows in child table refering the parent upon deleting a row in parent table
- ```
drop table employee;
create table employee (
emp_id number primary key,
emp_name varchar2(30),
emp_salary decimal(10,2),
dept_id number references department on delete cascade);
```
- ### inserting values into employee table
    - the last insert will throw an integrity error,because we dont have a department under id=5
    - ```
    insert into employee values(1,'Ahmad',40000,1);
insert into employee values(2,'Mark',35000,2);
insert into employee values(3,'Abid',50000,2);
insert into employee values(4,'Rahul',45000,1);
insert into employee values(5,'Uzma',30000,3);
insert into employee values(6,'Sean',40000,5);
```
- the correct insert statement is 
```
insert into employee values(1,'Ahmad',40000,1);
insert into employee values(2,'Mark',35000,2);
insert into employee values(3,'Abid',50000,2);
insert into employee values(4,'Rahul',45000,1);
insert into employee values(5,'Uzma',30000,3);
insert into employee values(6,'Sean',40000,4);
```
- upon deleting a row with dept_id=4 from department , the rows with dept_id=4 in employee table also will be deleted.
    - ```
    delete from department where dept_id=4
select * from employee;
``` 
- ### group by clause
    - used to group rows that have same values
    - used in select statement
    - optionally in conjunction with aggregate functions to produce summary reports from the db
    - ### create products and supplier tables
        - ```
        create table supplier(
supplier_id number primary key,
supplier_name varchar2(20));
create table products(
pro_id number primary key,
pro_name varchar2(20),
pro_price number,
supplier_id  number references supplier on delete cascade);
```
    - entering data into the tables
    ```
    insert into supplier values(1,'DELL');
insert into supplier values(2,'Lenovo');
insert into supplier values(3,'HP');
insert into supplier values(4,'Amazon');
insert into products values (1,'Mouse',100,2);
insert into products values (2,'Keyboard',150,1);
insert into products values (3,'HHD',1000,4);
insert into products values (4,'Mouse',200,1);
insert into products values (5,'Keyboard',100,2);
insert into products values (6,'Mouse',150,3);
```
- group by example
    - ```
    select pro_name from products group by pro_name;
    ```
- ### aggregate functions
    - calculate on a group of rows and return a single value for each group
    - group by divides the eows into groups and an aggregate function calculates and return a single result for each group
    - |Function|Action|
    |---|---|
    |count | return the number if rows in the column|
    |min| returns the minimum of the range of values|
    |max| return the maximum of the range of values|
    |avg|returns the average of the range of values|
    |stddev|return the standard deviation of the range of values|
    |variance|return the variance of the range of values|
    |sum|return the sum of the range of values|

- Aggregate
    - count of rows?
    ```
    select count(*) from employee;
    ```
    - minimum salary of the employees;
    ```
    select min(emp_salary) from employee;
    ```
    - maximum salary of the employees;
    ```
    select max(emp_salary) from employee;
    ```
    - average salary of the employees;
    ```
    select avg(emp_salary) from employee;
    ```
    - standard deviation of salary of the employees;
    ```
    select stddev(emp_salary) from employee;
    ```
    - variance of  salary of the employees;
    ```
    select variance(emp_salary) from employee;
    ```
    - sum salary of the employees;
    ```
    select sum(emp_salary) from employee;
    ```
    - get the count and salary  of employees grouped by salary
    ``` 
select emp_salary,count(*) from employee group by emp_salary
```
or 
```
select emp_salary,count(emp_salary) from employee group by emp_salary
```
- get the sum of differnt groups of salaries
```
select emp_salary,sum(emp_salary) from employee group by emp_salary
```
- ## having clause
    - used with group by clause to restrict group of returned rows where condition is TRUE
        - select the product name and its count grouped by their name
        ```
        select pro_name,count(*) from products 
group by pro_name;
```
        - select the product name and its count grouped by their name with count greater than 2
        ```
        select pro_name,count(*) from products 
group by pro_name having count(*) >2;
```
        - get the sum of pro_price grouped by product_name
        ```
        select pro_name,sum(pro_price) from products 
group by pro_name;
```
        - get the sum of pro_price grouped by product_name having sum>250
        ```
        select pro_name,sum(pro_price) from products 
group by pro_name having sum(pro_price)>250;
```
- ### character functions
    - function that takes one or more character values as parameters and returns either a character value or a number value
    - they are of two types
        - case conversion functions
            - lower => return the name of employee in lowercase
                ```
                select lower(emp_name) from employee;
                ```
            - upper => return the name of employee in uppercase
                ```
                select upper(emp_name) from employee;
                ```
            - initcap =>return the name of employee with first character capital
                ```
                select initcap(emp_name) from employee;
                ```
        - character manipulation functions
            - concat => concat name with salary of employee
            ```
            select concat(emp_name,emp_salary) from employee;
            ```
            - substr=>select 3rd and 4th character of employees
            ```
            select substr(emp_name,3,2) from employee;
            ```
            - length => select length of employee 
            ```
            select length(emp_name) from employee;
            ```
                - get employees with length(name)=5
                ```
                select emp_name from employee where length(emp_name)=5;
                ```
            - instr -> returns the location of the second string in the first string ,location start from 1, if not found returns 0
            ```
            select instr('I am Ahmad','Ahmad') from dual;
            ```
            - lpad | rpad -> lpad will return the left 2 character of the string passed, rpad gives the right 2.
            ```
            select lpad('Ahmad',2) from dual;
            select rpad('Ahmad',10,'0') from dual;
            ```
            - trim -> removes the spaces in front and end
            ```
            select trim(' Ahmad ',10,'0') from dual;
            ```
            - replace 
            ```
            select replace('123Ahmad123','123') from dual;
            ```
- ### NUMERIC FUNCTIONS
    - takes numeric input and returns  numeric values
    |function|Action|Example|Displays|
    |---|---|---|---|
    |CEIL(n)|returns nearest whole number greater than or equal to n|select CEIL(12.3) from dual;|13|
    |FLOOR(n)|returns nearest whole number less than or equal to n|select FLOOR(127.6) from dual;|127|
    |ROUND(n,m)|rounds n to m places to the right of the decimal point|select ROUND(579.34886,3) from dual;|579.349|
    |POWER(n,m)|multiplies m to the power n|select POWER(5,3) from dual;|125|
    |MOD(m,n)|Returns the remainder of the division of m by n,if n=0,then 0 if n>m then m is returned|select mod(6,7) from dual;|6|
    |SQRT(n)|returns the square root of n|select SQRT(9) from dual;|3|
    |ABS(n)|returns the absolute value of n|select ABS(-29) from dual;|29|
    |TRUNC(n1,n2)|returns a value with the required number of decimal places while a negative n2 rounds to the left of the decimal|select TRUNC(29.16,1),trunc(31.2,-1) from dual;|29.1,30|
- ### CONVERTION FUNCTIONS
    - converts a value (of anytype) into a specified datatype.
        - To_Date : converts a string into a date format.
        - To_Char: convert either a number or a date value to a string value.
        - NVL: Allows you to replace NULL value with another value
        - Decode:Use to expand small abbreviation.
- create a new employees table
```
create table employees(
emp_id number  PRIMARY key,
emp_name varchar2(20),
emp_salary decimal(10,2),
join_date date,
country varchar2(50));
```
    - insert values into employees
    ```
    insert into employees values(1,'Ahmad',5000,'1-jan-2020','PAK');
    ```
    - the following will throw format error
    ```
    insert into employees values(2,'Mark',50000,'2020-jan-1','USA');
    ```
    - this will be coverted  and inserted properly
    ```
insert into employees values(2,'Mark',50000,to_date('2020-jan-1','yyyy-mm-dd'),'USA');
```
    ```
insert into employees values(3,'Uzma',50000,to_date('2020-March-1','yyyy-mm-dd'),'UAE');
```
- select the name and month of joining 
```
select emp_name,to_char(join_date,'MM') from employees;
```
- replace null values of salary with 5000
```
select emp_name,NVL(emp_salary,5000) from employees;
```
- decode:
    - what decode does is,it expands the abbreviations.
```
select emp_name,decode(country,'PAK','PAKISTAN','USA','UNITED STATE OF AMERICA') from employees;
```
|emp_name|decode|
|---|---|
|Ahmad	|PAKISTAN|
|Mark|	UNITED STATE OF AMERICA|
|Uzma|(null)|