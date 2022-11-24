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
- how to call the above function?
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