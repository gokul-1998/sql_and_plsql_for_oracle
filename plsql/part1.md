# pl/sql

- create a tabel
```
create table emp(
emp_id number primary key,
emp_name varchar2(20),
emp_salary decimal(10,2)
);
```
- get details of the table
```
DESC emp
```
    - it will give us the description of the table
    - output

    |Name|Null?|Type|
|---|---|---|         
|EMP_ID|     NOT NULL| NUMBER|       
|EMP_NAME|           | VARCHAR2(20)| 
|EMP_SALARY|         | NUMBER(10,2)| 

- ### how to insert data inside a table
    ```
insert into emp values(1,'Ahmad',40000);
insert into emp values(2,'Mark',35000);
insert into emp values(3,'Abid',50000);
insert into emp values(4,'Rahul',45000);
```

- ### get all data from emp
    ```
    select * from emp;
    ```
- ### select detail of emp_id 4
    ```
    select * from emp where emp_id=4;
    ```
- ### select all employees earning above 35k
    ```
    select * from emp where emp_salary>35000;
    ```
- ### update emp id 4 from emp table as 50000 
    ```
    update emp set emp_salary='50000' where emp_id=4;
    ```
- ### increment salary of all employee by 500rs
    ```
    update emp set emp_salary=emp_salary+500 ;
    ```
- ## ORDER BY
    - ### order by clause is used to sort the data returned by the query in ascending or descending order.
    ```
    select column_list from table_name order by column asc|desc;
    ```
- TRUNCATE TABLE
    - used to delete data from a table faster than DELETE
    - it is similar to DELETE with no WHERE clause
    ```
    truncate table table_name;
    ```
- DROP TABLE
    - drop table along with data
    ```
    drop table table_name1,table_name2,etc...
- create 2 tables
    ```
    create table department1(
dept_id int not null primary key,
dept_name varchar2(50)
);
desc department1
create table employee1(
emp_id int not null primary key,
emp_name varchar2(20),
emp_salary decimal(10,2),--10 digits max with 2 decimals
dept_id int
);
```
- ### insert into department1  table
```
insert into department1 values(1,'IT');
insert into department1 values(2,'HR');
insert into department1 values(3,'MEDICAL');
insert into department1 values(4,'QUALITY');
```
- ### there are two wildcards in conjunction with LIKE operator
    - % represents zero or one or multiple characters
    - _ represents ine character

- insert data into employee table
```
insert into employee1 values(1,'Ahmad',40000,1);
insert into employee1 values(2,'Mark',35000,2);
insert into employee1 values(3,'Abid',50000,2);
insert into employee1 values(4,'Rahul',45000,1);
insert into employee1 values(5,'Uzma',30000,3);
insert into employee1 values(6,'Sean',40000,5);
```
- ### UNION combine two or more select queries results, removes duplicates
    - rules:
        - the number and the order of columns must be same in all queries
        - column must also have similar data types
    - UNION ALL wwill have the duplicates.

- COLUMN ALIAS -> temporary name for columns
- Table alias -> temporary name for table ,usually during joins.
    - ```
    select emp_name,emp_salary,dept_name from employee1,department1
where employee1.dept_id=department1.dept_id;
```
OR
    - ```
    select emp_name,emp_salary,dept_name from employee1 e,department1 d
where e.dept_id=d.dept_id;
```
- ### INNER JOIN
    - return only  those rows that have a match in both joined tables
    - 
    ``` 
    select column_list from table1 inner join table2 on table1.colname=table2.colname
    ```
    example:
    ```
    select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
    from employee1 e inner join department1 d
    on e.dept_id = d.dept_id
    ```
- LEFT JOIN 
    - returns all rows from left table and matching rows from right table
    - The result is NULL from rightside , if there is no match
    - 
    ```
    select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
    from employee1 e left join department1 d
    on e.dept_id = d.dept_id;
    ```
- RIGHT JOIN
    - return all the rows from right table and matching rows from left table
    - The result is null from the left side if there is no match
    ```
    select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
    from employee1 e right join department1 d
    on e.dept_id = d.dept_id
    ```
- FULL JOIN 
    - return all the rows form the joined table whether they are matched or not
    - combines left and right joins
    - a type of outer join ,also refered as full outer join  
    ```
    select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
    from employee1 e full join department1 d
    on e.dept_id = d.dept_id
    ```
- CROSS JOIN
    -specifies that all rows from first table join with all of the rows of second table.
    ```
    select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
    from employee1 e
    cross join department1 d;
    ```
- NATURAL JOIN
    - compare all common columns in both table and return all match record;
    -```
    select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
    from employee1 e
    natural join department1 d;
    ```
- SELF JOIN
    - join in which a table is joined with itself
    ```
    select e1.emp_id,e2.emp_name,e1.emp_salary
    from employee1 e1,employee1 e2
    where e1.emp_id=e2.emp_id;
    ```
- Table space and datafiles
    - oracle db stores logically in tablespace and physically in data file.
    |tablespace|datafiles|
    |---|---|
    |can belong to only one db|can belong to only one tablespace and one database|
    |consist of one or more data files|all data store under the data file physically|
    |Are further divided into logical units of storage|
    ```
    select tablespace_name from user_tablespaces;
    ```
- ### create User
```
CREATE USER username IDENTIFIED BY password
DEFAULT TABLESPACE tablespaceName
TEMPORARY TABLESPACE temp
QUOTA UNLIMITED ON tablespacename;
```
    - ### eg:  
    - note: password must have more than 8 characters and have numeric and a capital and small character
```
create user rahul identified by Gokul9842274639
default tablespace data
temporary tablespace temp
quota unlimited on data;
```
- ### create role
```
CREATE ROLE rolename;
```
    - ### eg:
```
CREATE ROLE DEMO;
```
- ### permission to role
```
GRANT CREATE TABLE,CREATE SESSION TO rolename;
```
    - ### eg:
    ``` 
GRANT CREATE TABLE , CREATE SESSION to DEMO;
```
- ### assign role to user
```
GRANT rolename TO username
```
    - ### eg
    ```
    GRANT DEMO TO rahul;
    ```
- create a new connection in oracle sql developer with above credentials and create a table for it
- ## alter tablespace
    - changes the description of the table space at the current server
- ### alter table statement
    - used to add delete or modify columns in an existing table
    - to add and drop various constraints in an existing table
    - ```
    desc employee1
alter table employee1 add emp_address varchar2(50);
desc employee1
```
    - added a new column
    - how to rename a column
        -  ```
        alter table employee1 rename column  emp_address to address;
        ```
    - change datatype of a column
    ```
    alter table employee1 modify address number;
    ```
    - modify the constraint on a table 
    ```
    alter table employee1 modify address unique;
    ```