- This portion follows -> https://youtu.be/xZEQ_64HCy4
- creating the table
    ```
    create table student2(
    student_id number unique,
    student_name varchar2(100 BYTE) not null,
    ssn varchar2(50 BYTE) primary key);
    ```
- creating a procedure to insert data in the table

    ```
    create or replace procedure add_student(p_student_id in student2.student_id%type,
    p_student_name in student2.student_name%type,
    p_ssn in varchar2,
    p_error_msg out varchar2)
    is
    -- declare ,this is optional,declare is not necessary and we can directly declare variables

    begin
        insert into student2(student_id,student_name,ssn)
        values(p_student_id,p_student_name,p_ssn);
        commit;
    exception
        when others then
            p_error_msg:=sqlerrm;

    end add_Student;
    ```
- here 'is/as' denotes that the parameters have been declared
- if we want to see all the stored procedures,functions,triggers,and packages user
    ```
    select * from user_source;
    ```
- how to test the stored procedures easily?
    - https://youtu.be/xZEQ_64HCy4?t=1369

- how to execute a stored procedure?
    ```
    set serveroutput on;
    declare
        v_error_msg varchar2(4000);
    begin
        add_student(256171285,'sai','aaa1',v_error_msg);
        dbms_output.put_line('error message is :'|| v_error_msg);
    end;
    ```
- note : set serveroutput on ; is essential to get the output on screen.

- create a procedure to delete student using id
    ```
    create or replace procedure del_student(p_student_id in out student2.student_id%type,
    p_error_msg out varchar2)
    is 

    begin
        delete from student2 where student_id=p_student_id;
        if sql%rowcount=0 then
            raise_application_error(-20001,'student not found for'||p_student_id);
        end if;
        commit;
    exception
        when others then
            p_error_msg:= sqlerrm;
    end;
    ```
- how to call the above procedure?
    ```
    DECLARE
        V_ERROR_MSG VARCHAR2(4000);
        V_STUDENT_ID NUMBER:=256785;
    BEGIN
        DEL_STUDENT(V_STUDENT_ID,V_ERROR_MSG);
        DBMS_OUTPUT.PUT_LINE(V_ERROR_MSG);
    END;
    ```
    - NOTE we cant use the id(19229) directly on the del_student(x,y) because its an in out variable, so we have to initialize a variable and pass it as parameter
- # function
- function to check if the employee salary is greater than the average salary
    ```
    create or replace function check_sal(p_emp_no in employee1.emp_id%type)
    return number
    is 

    v_avg_sal employee1.emp_salary%type;
    v_sal employee1.emp_salary%type;
    v_dept_id employee1.dept_id%type;
    begin
    ---- finding dept_id and sal of an employee
    select emp_salary,dept_id into v_sal,v_dept_id
    from employee1
    where emp_id=p_emp_no;

    select avg(emp_salary)
    into v_avg_sal 
    from employee1
    where dept_id=v_dept_id;

    if v_sal>v_avg_sal then
    return 1;
    else
    return 0;
    end if;

    exception
    when no_data_found then
        return -1;
    when others then
        return null;
    end check_sal;
    ```
- it can be invoked from select statement if it does not contain any DML/ddl statement in it.
means it dont change any data in db.
- note: function should always return to a variable
- CALLING THE ABOVE DECLARED FUNCTION
    ```
    declare
        v_func_op number;
    begin
        v_func_op:=check_sal(4);
        DBMS_OUTPUT.PUT_LINE(V_FUNC_OP);
    END;
    ```
- using the above function in a select statement
<br>
    ```
    SELECT EMP_NAME,EMP_SALARY,CHECK_SAL(EMP_ID)
    FROM EMPLOYEE1;
    ```
- find the employee names whose salary is greater than the avg salary in their dept
    ```
    SELECT EMP_NAME,EMP_SALARY,CHECK_SAL(EMP_ID)
    FROM EMPLOYEE1
    where check_sal(emp_id)=1;
    ```
- note : aliases cannot be referred in where clause, but can only be referred in order by clause

- # packages
    - if we have some details in package specification they are publicly visible,
    - if we have something in body then they are private.

    ```
    create or replace package myfirst_pkg is
    v_comm number:=0.1;

    procedure del_student(p_student_id in out student2.student_id%type,
    p_error_msg out varchar2);

    function check_sal(p_emp_no in employee1.emp_id%type)
    return number;
    -- in this case both the above function and procedure are public

    end myfirst_pkg;
    -- the above are specification.
    -- without specification we cant define body, but without body we can define specification 
    create or replace package body myfirst_pkg is
    v_comm number:=0.1;

    procedure del_student(p_student_id in out student2.student_id%type,
    p_error_msg out varchar2)
    is 

    begin
        delete from student2 where student_id=p_student_id;
        if sql%rowcount=0 then
            raise_application_error(-20001,'student not found for'||p_student_id);
        end if;
        commit;
    exception
        when others then
            p_error_msg:= sqlerrm;
    end;

    function check_sal(p_emp_no in employee1.emp_id%type)
    return number
    is 

    v_avg_sal employee1.emp_salary%type;
    v_sal employee1.emp_salary%type;
    v_dept_id employee1.dept_id%type;
    begin
    ---- finding dept_id and sal of an employee
    select emp_salary,dept_id into v_sal,v_dept_id
    from employee1
    where emp_id=p_emp_no;

    select avg(emp_salary)
    into v_avg_sal 
    from employee1
    where dept_id=v_dept_id;

    if v_sal>v_avg_sal then
        return 1;
    else
        return 0;
    end if;

    exception
        when no_data_found then
            return -1;
        when others then
            return null;
    end check_sal;

    procedure add_student(p_student_id in student2.student_id%type,
    p_student_name in student2.student_name%type,
    p_ssn in varchar2,
    p_error_msg out varchar2)
    is
    -- declare ,this is optional,declare is not necessary and we can directly declare variables

    begin
        insert into student2(student_id,student_name,ssn)
        values(p_student_id,p_student_name,p_ssn);
        commit;
    exception
        when others then
            p_error_msg:=sqlerrm;

    end add_Student;

    end myfirst_pkg;
    ```