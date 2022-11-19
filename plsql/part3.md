# SQL COMMANDS TYPES
- ### Data Definition Language(DDL): 
    - to define the db, like
        - Create
        - drop
        - truncate (delete the data in table)
        - alter
        - backupdatabase
- ### Data Manipulation Language(DML):
    to manipulate the data in database like
        - Use
        - insert
        - update
        - delete
        - select
    - Apart from these , other manupulative operators/functions such as
        - operators
        - aggregate functions
        - null functions
        - aliases & case statements
- ### Data control Language(DCL):
    - deals with user permissions and controls of database system like
        - PrivilegeName-> privilege/right/access granted to the user.
        - Objectname ->name of database object like TABLE/VIEW/STORED PROC
        - UserName -> name of user who is given the access/rights/privileges
        - PUBLIC- to grant access to all users
        - RoleName -> the name of a set of privileges grouped together
        - WITH GRANT OPTION - to give the user access to grant other users with rights.
- ### Transaction Contril Language(TCL)
    - Deal with the transaction of the database like,
        - COMMIT
        - ROLLBACK
        - SAVEPOINT
- ### Sequence in oracle database
    - syntax
        - ```
        create sequence schema_name.sequence_name
        [increment by interval]
        [start with first_number]
        [MAXVALUE max_value| NOMAXVALUE]
        [MINVALUE min_value | NOMINVALUE] 
        [CYCLE|NOCYCLE]
        [CACHE cache_size|NOCACHE]
        [ORDER|NOORDER];
        ```
        - ```
    create table students(std_id number primary key,
std_name varchar2(20));
create sequence std_seq increment by 1 start with 100
maxvalue 999 minvalue 1 nocycle nocache noorder;
insert into students values(std_seq.nextval,'Uzma');
select std_seq.currval from students;
select * from students;
```
        - the above sql creates a students table,
    it creates a sequence, and it auto fills the value while inserting data in the table
- ### Pseudocolumns
    - A pseudocolumn behaves like a table column, but is not actually stored in the table.
    - You can select from pseudocolumns but we cannot insert,update or delete their values.
    - used to get system-related data
    - Helps to get the metadata of our database
    - Allows us to get the details of access rights on our db
    - helps us to keep a check on our db/
        - SYSDATE: returns the current system date
        ```
        select sysdate from dual
        ```
        - SYSTIMESTAMP: returns the current system date with time, millisecond and AM,PM,etc.
        ```
        select systimestamp from dual;
```
        - UID: return the current Used ID
        - USER: returns the current user details.
        - ROWID : return the rowid of a row in a database table in binary format
        - ROWNUM : returns the rownum from 
        ```
        select rownum,emp_name from employee1;
        select emp_salary,emp_name from employee1 where rownum<3;
        ```
        - CURRVAL : CUrrent value
        - NEXTVAL: next value
- ### Subquery,Innerquery or nested query
    - a subquery is a query within a query
    - used to select record where we do not know the condition values.
    - mostly we are using after where clause but we can use with from and select clause
    - oracle allows upto 255 levels of subqueries in the where clause
    - select the employees getting maximum salaries
        - ```
        select * from employee1 where emp_salary=(
select max(emp_salary) from employee1);
``` 
    - select * from employees earning above 300000
        - ```
        select * from employee1 where emp_salary in(
select emp_salary from employee1 where emp_salary>30000);
```
        - note the 'in' and '=' in the queries.
        - '=' used  when we return a single value from inner query
        - 'in' is used when multiple values are fetched from inner query 
- ### Data dictionary
    - contains all the info about the structures and objects of the database such as:
        * tab -> gives all the type detail of tables and views
        ```
        select * from tab;
        ```
        - all tables -> all_tables gives you information about all the tables that you have access to.
        ```
        select * from all_tables;
        select * from all_sequences;
select * from all_views;
        ```
        - all sequences
        - all views
        - all constraints,constants,etc
    - the data stored in the data dictionary are often called metadata
- ### generate a report
    - ```
    set pagesize 10
set linesize 150
set pause on
set pause 'Press any key..ssds'
ttitle left 'employee report'
btitle left 'end report'
column emp_name format as trunc
select e.emp_id,e.emp_name,e.emp_salary,d.dept_name
from employee1 e,department1 d
where e.dept_id=d.dept_id;
set pause off
set linesize 80
set pagesize 24
```
- to generate the report click in the query and execute script
- same with alias column names
```
set pagesize 10
set linesize 150
set pause on
set pause 'Press any key..ssds'
ttitle left 'employee report'
btitle left 'end report'
column emp_name format as trunc
select e.emp_id "EMP ID",e.emp_name "EMPNAME",e.emp_salary "EMPLOYEE SALARY",d.dept_name
from employee1 e,department1 d
where e.dept_id=d.dept_id;
set pause off
set linesize 80
set pagesize 24
```
- saving the generated report as a txt file
```
spool E:\emp.txt
set pagesize 10
set linesize 150
set pause on
set pause 'Press any key..ssds'
ttitle left 'employee report'
btitle left 'end report'
column emp_name format as trunc
select e.emp_id "EMP ID",e.emp_name "EMPNAME",e.emp_salary "EMPLOYEE SALARY",d.dept_name
from employee1 e,department1 d
where e.dept_id=d.dept_id;
set pause off
set linesize 80
set pagesize 24
spool off
``` 
- ## views in oracle
    - creating a view 
    ```
    create view employee_view as
select emp_name,emp_salary from employee1;
``` 
    - replacing an existing view
    ```
    create or replace view employee_view as
select* from employee1;
```
```
insert into employee_view values(8,'Nadee',40000,1,121);
```
- creating views with check option.
- The WITH CHECK OPTION clause can be given for an updatable view to prevent inserts to rows for which the WHERE clause in the select_statement is not true.
    - https://www.oracletutorial.com/oracle-view/oracle-with-check-option/
    - https://www.mysqltutorial.org/mysql-view-with-check-option/
    ```
    create or replace view employee_view as
select* from employee1 where emp_salary=40000 with check option;
```
    - delete a view
        ```
        drop view employee_view;
        ```
- ## input variable
    - create a prompt to accept input and store in ename variable
    ```
    accept ename char prompt 'Please enter employee name:'
    ```
    - accept ename from prompt and retrieve the data from the table,
    execute one line at a time
    ```
    accept ename char prompt 'Please enter employee name:'
select * from employee1 where emp_name='&ename';
```
- ## define variable
    - variable takes value 6
```
define eid=6;
select * from employee1 where emp_id=&eid;
```
    - list all the variables:
    ```
    define
    ```
    - undefine a variable?
    ```
    undefine eid
    ```