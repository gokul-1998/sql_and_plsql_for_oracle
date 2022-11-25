this file follows -> https://youtu.be/n4XuFAOCa2s?list=PLMz9xkBNTr6pT8KcJpgVkMq521ABGMSxF

- query to check if the average salary of the employee is grearter than the emp_sal or not using the package
```
select emp_name,myfirst_pkg.check_sal(emp_id)
from employee1;
```
- how to call packaged procedure
```
declare
V_ERROR_MSG VARCHAR2(4000);
        V_STUDENT_ID NUMBER:=2561785;
begin
    myfirst_pkg.del_student(V_STUDENT_ID,V_ERROR_MSG);
end;
```
- we cannot call the add student procedure, because its a private instance as its only declared inside of the package body

- ## oracle supplied packages in application development

- UTL_FILE -> R/W TO FILE
- DBMS_OUTPUT
- UTL_MAIL
- DBMS_ALERT
- DBMS_LOCK
- DBMS_SESSION
- DBMS_APPLICATION_INFO
- HTP
- DBMS_SQL -> NATIVE DYNAMIC SQL,EXECUTE ANY DDL STATEMENT
- DBMS_SCHEDULER

-  How to get all the packages?

```
select * from all_objects;
```
```
select * from all_objects where owner='SYS' and object_type='PACKAGE';
```

- ## DYNAMIC SQL
- in procedures and function we can only the DML,we cant write DDL like DROP,CREATE,ALTER,etc.. directly in a procedure.

- how to embbed a ddl in dynamic sql

- native_dynamic_sql
-> using `execute immediate`
- dbms_sql -> execute ddl

- create a table which consists of table_name,index_name,column_name, type of index, and the date created
```
create table dbindexes(
table_name varchar2(100),
index_name varchar2(100),
column_name varchar2(100),
type varchar2(100),
create_dt date
);
```
- insert into dbindexes values
```
insert into dbindexes values('student2','idx_name','student_name','normal',sysdate);
commit;
```
- procedure to create indexes on a table using dynamic sql

```
create or replace procedure create_indexes(
p_table_name in varchar2,
p_error_out out varchar2)
is
    v_sql varchar2(32000);
    v_index_name varchar2(100);
    v_column_name varchar2(100);
    
    cursor cur_indexes
    is 
    select table_name,index_name,column_name
    from dbindexes
    where table_name=p_table_name;
begin
    -- creating the indexes in loop
    for ind in cur_indexes
    loop
        v_index_name:=ind.index_name;
        v_column_name:=ind.column_name;
        v_sql:='create index '||v_index_name|| ' on ' || p_table_name || '('||v_column_name || ')';
        dbms_output.put('v_sql:'||v_sql);
        execute immediate v_sql;
    end loop;
exception
    when others then
        p_error_out:=sqlerrm;
end create_indexes;
```

- how to create index on a column ?
```
create index index_name on table(column_name);
```
- calling the package procedure
```
set serveroutput on;
declare
    v_error_msg varchar2(4000);
begin
    myfirst_pkg.create_indexes('student2',v_error_msg);
    dbms_output.put_line(v_error_msg);

end;
```