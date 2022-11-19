- ## plsql exception handling
    - an error occurs during execution of program
    - they are of two types
        - pre-defined exceptions
        - user defined exceptions
    - ## pre-defined exceptions
        - there are many pre-defined exception in PL/SQL which are executed when any database rule is violated by the programs
        - syntax
        ```
        declare
            <declaration section>
        begin
            <executable command(S)>
        exception
            <exception handling goes here>
            when exception1 then
                exception-handling statements
            when exception2 then
                exception2-handling statements
            ............
            when others then
                exception3-handling-statements
        end;
        ```
        - list of some pre-defined exceptions:
        |exception|oracle error|sql code|description|
        |---|---|---|---|
        |access_int_null|06530|-6530|it is raised when a null object is automatically assigned a value.|
        |case_not_found|06592|-6592|none of the when clause in case statement satisfied and no else clause us specified.|
        |collection_is_null|06531|-6531|using collection methods(except exists) or accessing collection attributes on a uninitialized collections|
        |dup_val_on_index|00001|-1|it is raised when duplicate values are attemplted to be stored in a column with unique index|
        |invalid_cursor|01001|-1001|illegal cursor operations like closing an unopened cursor|
        |invalid_number|01722|-1722|conversion of character to a number failed due to invalid number character|
        |login_denied| 01017|-1017| it is raised when a program attempts to log on to the database with invalid credentials.|
        |no_data_found|01403|+100|it is raised when a select into statement return no rows|
        |not_logged_on|01012|-1012|it is raised when a database call is issued without being connected to the database.|
        |program_error|06501|-6501|it is raised when pl/sql has an internal problem|
        |rowtype_mismatch|06504|-6504|it is raised when a cursor fetched value in a variable having incompatible data type.|
        |self_is_null|30625|-30625|it is raised when a member method is invoked but the instance if the object type was not initialized
        |storage_error|06500|-6500|it is raised when pl/sql ran out of memory was corrupted|
        |too_many_rows|01422|-1422|it is raised when a select into statement returns more than one row|
        |value_error|06502|-6502|arithmetic or size constraint error(eg:assigning a value to a variable that is larger than the variable size)|
        |zero_divide|01476|1476|it is raised when an attempt is made to divide a number by zero|
        - returns the name of the employee
        ```
        declare
e_id employee1.emp_id%type;
e_name employee1.emp_name%type;
begin
select emp_name into e_name from employee1 where
emp_id=&e_id;
dbms_output.put_line(e_name);
end;
```
        - when we enter a number which is not present in table.it throws no_data_found error.Below query handles such error
    ```
    set serveroutput on;
declare
e_id employee1.emp_id%type;
e_name employee1.emp_name%type;
begin
select emp_name into e_name from employee1 where
emp_id=&e_id;
dbms_output.put_line(e_name);
exception when no_data_found then
dbms_output.put_line('No record found');
end;
```
    - the below sql handles the other errors
    ```
    --set serveroutput on;
declare
e_id employee1.emp_id%type;
e_name employee1.emp_name%type;
begin
select emp_name into e_name from employee1 where
emp_id=&e_id;
dbms_output.put_line(e_name);
exception when no_data_found then
dbms_output.put_line('No record found');
when others then
dbms_output.put_line('Error!!');
end;
```
- ## raising exceptions
    - all predefined exceptions are raised implictly whenever the error occurs.
    - user-defined exceptions need to be raised explicitly.
    - this can be achieved using the keyword 'RAISE' .This can be used in any of the ways mentioned below.
    - syntax for raising an exception:
```
declare
    exception_name exception;
begin
    if condition then
        raise exception_name;
    end if;
exception
    when exception_name then
    statement;
end;
```
    - example
    ```
    declare 
n number :=&n;
ex_invalid exception;
begin
if n>5 then
raise ex_invalid;
else
dbms_output.put_line('n is less than 5');
end if;
exception when ex_invalid then
dbms_output.put_line('N is greater than 5');
end;
```
    - ## user-defined exceptions
        - pl/sql facilitates their users to own exceptions according to the need of the program.
        - a user defined exception can be raised using either RAISE statement or the procedure DBMS_STANDARD.RAISE_APPLICATION_ERROR
    - syntax
        ```
        declare
        <exception_name> exception;
        begin
        <execution block>
        exception
        when <exception_name> then
        <handler>
        end;
        ```
    - example
    ```
    set serveroutput on;
declare 
e_id employee1.emp_id%type:=&e_id;
e_name employee1.emp_name%type;
ex_invalid exception;
begin
if e_id<=0 then raise
ex_invalid;
else 
select emp_name into e_name from employee1 where emp_id=e_id;
dbms_output.put_line(e_name);
end if ;
exception when ex_invalid then
dbms_output.put_line('ID must be greater than 0');
when no_data_found then
dbms_output.put_line('no such employee');
when others then
dbms_output.put_line('error');
end; 
```
- ## pl/sql package
    - schema object that contains definitions for a group of related functionalities
    - includes variables,cursors,constants,exception,procedure,functions,etc..
    - package is compiled and stored as a database object that can be used later.
- componenets of a package
    - package specification
    - package body
- package specification
    - paclage specification consist of declaration of all the public variables,cursors,objects,procedures,functions,exception
    - syntax
    ```
    create [or replace] package <package_name>
    is 
    <sub_program and public element declaration>
    end < package_name>;
- package body
    - consist of the definition of all the elements that are present in the package specification
- syntax
    ```
    create [or replace] package body
    <package_name>
    is
    <global_declaration part>
    <private element and public element definition>
    <package initialization>
    end <package_name>;
    ```
- create a package
    ```
    create or replace package package_department
as 
procedure insert_department(dept_id in number,dept_name in varchar2);
procedure delete_department(delete_id in number);
function add_number(n1 in number,n2 in number)
return number ;
end;
```
    - package has been  be declared vaguely in the above query and then we are defining the body separately.
    ```
    create or replace package body package_department
as
procedure insert_department(dept_id in number,dept_name in varchar2)
is 
begin
insert into department1 values(dept_id,dept_name);
end insert_department;
procedure delete_department(delete_id in number)
is 
begin
delete from department1 where dept_id=delete_id;
end delete_department;
function add_number(n1 in number,n2 in number)
return number
is 
n3 number;
begin 
n3:=n1+n2;
return n3;
end add_number;
end package_department;
```
- voila we can exceute the function in package
```
select * from department1;
set serveroutput on;
begin 
package_department.insert_department(1111,'manage');
end;
```
- calling the delete from package
```
select * from department1;
set serveroutput on;
begin 
package_department.delete_department(1111);
end;
```
- executing the addition function
```
declare 
n3 number;
begin
n3:=package_department.add_number(50,20);
dbms_output.put_line('addition:'||n3);
end;
```
                 