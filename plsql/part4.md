## pl/sql
    - procedural language extension of SQL
    - supports both static and dynamic sql
    - allows sending an entire block of statements to the database at one time
    - saves time on design and debugging by strong features as exception handling,
    - Encapsulation,data hiding and Object oriented data types
    - high security level
    - provides access to predefined sql packages
    - supports structured programming through functions and procedures
    - supports development of web apps and server pages
- ### block structure
```
        declare (optional)
            <declaration section>
        begin (mandatory)
            <executable section> needs atleast one command
        exception (optional)
            <exception handling>
        end; 
```
- to make the output visible on the console 
    ``` 
set serveroutput on;
```
- display welcome message
```
declare 
message varchar2(50);
begin
message:='Welcome to iitm ';
dbms_output.put_line(message);
end;
```
- take variables name and salary from a query and print them

```
declare 
name employee1.emp_name%type;
-- this line declares the type of name variable as the type corresponding to emp_name in employee1 table
salary employee1.emp_salary%type;
msg number;
begin
select emp_name,emp_salary into name,salary from employee1 
where emp_id=&msg;
dbms_output.put_line(name);
dbms_output.put_line(salary);
end;
--set serveroutput on;
```
- ## if-else statement
    ```
    set serveroutput on;
declare
e_sales number:=50000;
e_commission number:=0;
begin
if e_sales>40000 then
e_commission :=(e_sales*10)/100;
dbms_output.put_line('Your commission is 10% equal to:'||e_commission);
end if;
end;
```
- with else part
```
set serveroutput on;
declare
e_sales number:=30000;
e_commission number:=0;
begin
if e_sales>40000 then
e_commission :=(e_sales*10)/100;
dbms_output.put_line('Your commission is 10% equal to:'||e_commission);
else
dbms_output.put_line('Your commission is 2% equal to:'||(e_sales*2)/100);
end if;
end;
```
- with elseif condition
```
set serveroutput on;
declare
e_sales number:=35000;
e_commission number:=0;
begin
if e_sales>40000 then
e_commission :=(e_sales*10)/100;
dbms_output.put_line('Your commission is 10% equal to:'||e_commission);
elsif e_sales<=40000 and e_sales>30000 then
dbms_output.put_line('Your commission is 5% equal to:'||(e_sales*5)/100);
else
dbms_output.put_line('Your commission is 2% equal to:'||(e_sales*2)/100);
end if;
end;
```
- ## basic loop
    - execute statements until condition is met
    - the body of loop must contain atleast one exit or exit when statement fpr terminating the loop
    ```
    <<label>>loop
    statements;
    end loop loop_label;
    ```
    - loop to print 1-10
    ```
    set serveroutput on;
declare
i number:=0;
begin 
loop
i :=i+1;
exit when i>10;
dbms_output.put_line('value of i:'||i);
end loop;
end;
```
- same output as above ,but we use if then loop to stop the iteration
```
set serveroutput on;
declare
i number:=0;
begin 
loop
i :=i+1;
if i>10 then
exit;
end if;
dbms_output.put_line('value of i:'||i);
end loop;
end;
```
- nested loops 
```
set serveroutput on;
declare 
i number :=0;
j number :=0;
begin 
<<outer_loop>> loop
i:=i+1;
exit outer_loop when i>2;
dbms_output.put_line('the outer loop:'||i);
j:=0;
<<inner_loop>>loop
j:=j+1;
exit inner_loop when j>3;
dbms_output.put_line('the inner loop:'||j);
end loop inner_loop;
end loop outer_loop;
end;
```
- ## while loop
    - execute one or more statements while a condition is true
    - syntax
    ```
    while condition
    loop
    statements;
    end loop;
    ```
- while loop to print numbers from 1 to 10
```
set serveroutput on;
declare i number:=1;
begin 
while i<=10
loop
dbms_output.put_line('value of i :'||i);
i:=i+1;
end loop;
end;
```
- can give condition to stop inside the while loop
```
set serveroutput on;
declare i number:=1;
begin 
while i<=10
loop
dbms_output.put_line('value of i :'||i);
i:=i+1;
exit when i=3;
end loop;
end;
```
- multiplication table for 200 using while loop
```
set serveroutput on;
declare 
var1 number;
var2 number;
begin
var1:=200;
var2:=1;
while var2<=10
loop
dbms_output.put_line(var2 ||' * '|| var1 ||' is: '||var1*var2);
var2:=var2+1;
end loop;
end;
```
- ## for loop
    - executes statements for a predetermined number of times
    - syntax
    ```
    for index in intial_value..final_value loop
    loop statements;
    end loop;
    ```
    - use for loop to print values from 1 to 5
    ```
    set serveroutput on;
begin
for i in 1..5 loop
dbms_output.put_line('value of i :'||i);
end loop;
end;
```
    - using for loop do some multiplications
    ```
    set serveroutput on;
declare 
var1 number;
begin
var1:=100;
for var2 in 1..10 loop
dbms_output.put_line(var1*var2);
end loop;
end;
```
    - loop through employee table and print the names of employees with salary=40000
    ```
    set serveroutput on;
begin
for e in (select * from employee1) loop
if e.emp_salary=40000 then
dbms_output.put_line('id:'||e.emp_id||',name:'||e.emp_name||',salary'||e.emp_salary);
end if; 
end loop;
end;
```
- ## pl/sql cursor
    - when an sql is processed,oracle creates a memory area known as context area.
    - cursor is a pointer to this context area.
    - contains all info for processing the statement
    - context area is controlled by cursor.
    - cursor used for processing ROW-by-Row
    - they are of 2 types
        - implicit
        - explicit
    - ## cursor attributes
        - to work with cursor whether implicit or explicit cursor, the following are used.
        |attribute name|description|
        |---|---|
        |%isopen|if the cursor is open , it returns a boolean value TRUE otherwise FALSE|
        |%FOUND|if record fetched by cursor was successful|
        |%NOTFOUND|if the record fetched by cursor was unsuccessful|
        |%ROWCOUNT|returns no of rows affected by pl/sql statement|
    - pl/sql implicit cursors
        - are automatically generated by oracle while an sql statement is executed
        - these are created by default o process the statements when DML statements like INSERT,UPDATE,DELETE etc are executed.
    - update the salary in employee1 table
    ```
    set serveroutput on;
declare
total_rows number;
begin
update employee1 set emp_salary=emp_salary-5000;
if sql%notfound then 
dbms_output.put_line('Record not updated');
elsif sql%found then
total_rows:=sql%rowcount;
dbms_output.put_line('total rows updated:'||total_rows);
end if;
end;
```
- plsql explicit cursors
    - defined by programmers to gain control over context area.
    - these cursors should be defined in the declaration secrion of pl/sql block
    - it is created on select statement which returns more than one row.
    - syntax to create explicit cursor
        - declare cursor to initialize in the memory
        - open the cursor to allocate memory
        - fetch the cursor to retrieve data
        - close the cursor to release allocated memory
    - ## delcare the cursor
        - defines the cursor with a name and the associated select statement
        - syntax for explicit declaration
            ```
            cursor name is
            select statement;
            ```
    - ## open the cursor 
        - used to allocate memory for the cursor and make it easy to fetch the rows returned by sql statements into it
        - syntax for cursor open
            ```
            OPEN cursor_name;
            ```
    - ## Fetch the cursor
        - used to access one row at a time. You can fetch rows from the above-opened cursor as follows
        - syntax for cursor fetch :
            ```
            fetch cursor_name into variable_list;
            ```
    - ## close the cursor:
        - it is used to release the allocated memory.The following syntax is used to close the above-opened cursors.
        - syntax for cursor close:
        ```
        close cursor_name;
        ```
    - ## using cursor to fetch the data
    ```
    set serveroutput on;
declare 
e_id employee1.emp_id%type;
e_name employee1.emp_name%type;
e_salary employee1.emp_salary%type;
cursor e_employee is
select emp_id,emp_name,emp_salary from employee1;
begin
open e_employee;
loop
fetch e_employee into e_id,e_name,e_salary;
exit when e_employee%notfound;
dbms_output.put_line(e_id||': '||e_name||': '||e_salary);
end loop;
close e_employee;
end;
```
- # triggers
    - triggers are stored programs
    - automatically executed or fired (before/after) occurence of some events.
        - ### events are
            - DML (delete,insert or update)
            - DDL (create,alter or drop)
            - database operation (servererror,logon,startup or shutdown)
    - create a new table
    ```
    create table new_employee(
id number primary key,
salary decimal(10,2),
message varchar2(20));
select * from new_employee;
```
    - creating trigger
    ```
    create trigger trigger_emp
after update of emp_salary on employee1
for each row 
begin
insert into new_employee(id,salary,message)
values(:old.emp_id,:new.emp_salary,'record is updated');
end;
```
    - checking if  trigger worked
    ```
     update employee1 set emp_salary=10000 where emp_id=1;
 select * from employee1;
 select * from new_employee;
 ```
- ## pl/sql procedure
    - a block which perform one or more specific tasks
    - mainly created to perform one or more DML operation over Database.
    - syntax:
        ```
        create [or replace] procedure procedure_name
        [(parameter_name [in|out|in out])] type[....])
        {is|as}
        begin
        <procedure_body>
        end procedure_name;
        ```
    - create a procedure
    ```
    create or replace procedure welcome
as
begin
dbms_output.put_line('Welcome to iitm');
end;
```
    - calling a procedure
    ```
    set serveroutput on;
begin
welcome;
end;
```
    - procedure to insert a department
    ``` 
create or replace procedure insert_dept(
dept_id in number,dept_name in varchar2)
is 
begin
insert into department1 values(dept_id,dept_name);
end;
```
    - update employee
    ```
    select * from employee1;
create or replace procedure update_emp(
id in number,amount in number,d out number)
is
begin
update employee1 set emp_salary=emp_salary+amount
where emp_id=id;
select emp_salary into d from employee1 where emp_id=id;
dbms_output.put_line(d);
end update_emp;
```
    - executing the update procedure,we have k as the third param which will return the output
    ```
    variable k number;
execute update_emp(1,10000,:k);
```
- ## pl/sql function
    - pl/sql function is very similar to pl/sql procedure but a function must return a value.
    - use return to return the value
    - return datatype is mandatory at the time of creation
    - used mainly to perform some calculation
    - ### syntax:
    ```
    create [or replace] function function_name
    [(parameter_name [in|out|in out])] type[....]
    return return_datatype
    {is|as}
    begin
    <function body>
    end function_name;
    ```
    - creating a function
        ```
    create or replace function add_number(
n1 in number,n2 in number)
return number
is 
n3 number;
begin
n3:=n1+n2;
return n3;
end add_number;
```
    - calling the add function
    ```
    declare n3 number;
begin
n3:=add_number(50,20);
dbms_output.put_line('Addition is ' || n3);
end;
```
- create a function to update salary
```
create or replace function update_sal(
id in number,amount in number)
return number 
is 
d number;
begin
update employee1 set emp_salary=emp_salary+ amount
where emp_id=id;
select emp_salary into d from employee1 where emp_id=id;
dbms_output.put_line('new salary: '|| d);
return d;
end;
```
- calling the update_sal function
```
declare
d number;
begin
d:=update_sal(3,10000);
end;
```
