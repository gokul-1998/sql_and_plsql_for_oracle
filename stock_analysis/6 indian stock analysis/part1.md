- Two of India's biggest stock exchanges BSE and NSE, collectively clear trades combining to greater than 40,000 crores every day. 
- As you might already be aware, a lot of trading happens on the basis of technical and fundamental analysis.

- One of the most basic technical analysis used by a lot of stock traders is the `Moving Average` Method. Consider the following price trend of a particular stock.
<br></br>
![image](https://user-images.githubusercontent.com/82328858/204083665-965bfa2c-fe0d-4fd2-98e6-612e671ccf56.png)

- The given image is an example of Moving average calculation.
- Here we are doing 5 days and 10 days moving average claculation.
- As We can see, Moving average use the past data to smoothen the price curve. For the purpose of this assignment, we will be using 20 Day and 50 Day moving averages.
- Now that we know about the concept of Moving average, we shall be wondering how to use it to determine whether to buy or sell a stock.
    - When the `shorter-term moving average crosses above the longer-term moving average`, it is a signal to `BUY`, as it indicates that the trend is `shifting up`. This is known as a `Golden Cross`
    - On the opposite `when the shorter term moving average crosses below the longer term moving average`, it is a signal to `SELL`, as it indicates the `trend is shifting down`. It is sometimes referred to as the `Death Cross`.
    - Note that it is important that `the Moving Averages Cross each other in order to generate a signal`. Merely being above or below is `not sufficient to generate a signal`.
    - When the `signal is neither buy nor sell`, it is classified as `hold`. If we already own the stock, keep it and if we don't then don't buy it now. 

- first i am importing the csv data into oracle database in 6 different tables namely:
    - bajaj
    - eicher
    - hero
    - infosys
    - tcs
    - tvs
<br></br>
- creating table
    ```
    create  table EICHER(
    stk_date date,
    close number(10,2));
    ```
- after creating the table, import the data 
- now we are creating a new table with 20 and 50 days MA
```
CREATE TABLE bajaj1 as (
SELECT stk_date, ROUND(Close,2)  "Close Price", 
ROUND(AVG(Close) OVER(ORDER BY stk_date ROWS 19 PRECEDING),2)  "20 Day MA",
ROUND(AVG(Close) OVER(ORDER BY stk_date ROWS 49 PRECEDING),2)  "50 Day MA"
FROM bajaj);
```
- similarly create for all the 6 tables

now lets unify all the tables

```
CREATE TABLE master_stocks AS
SELECT stk_date ,bajaj.Close  Bajaj, eicher.Close Eicher,
hero.Close Hero, infosys.Close Infosys, 
tcs.Close TCS, tvs.Close TVS
FROM bajaj
INNER JOIN eicher
USING(stk_date)
INNER JOIN hero
USING(stk_date)
INNER JOIN infosys
USING(stk_date)
INNER JOIN tcs
USING(stk_date)
INNER JOIN tvs
USING(stk_date);

```
- using case statement with row_number()
```
select stk_date,
(case row_number() over(order by stk_date) when 2 then 'low' end) from bajaj1 where stk_date like '05-01%';
```

- there is something called as lag function 
https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions070.htm#:~:text=LAG%20is%20an%20analytic%20function,offset%20prior%20to%20that%20position.

- It provides access to more than one row of a table at the same time without a self join. Given a series of rows returned from a query and a position of the cursor, LAG provides access to a row at a given physical offset prior to that position.
- If you do not specify offset, then its default is 1. The optional default value is returned if the offset goes beyond the scope of the window. If you do not specify default, then its default is null.

- You cannot use LAG or any other analytic function for value_expr. That is, you cannot nest analytic functions, but you can use other built-in function expressions for value_expr.

```
SELECT last_name, hire_date, salary,
   LAG(salary, 1, 0) OVER (ORDER BY hire_date) AS prev_sal
   FROM employees
   WHERE job_id = 'PU_CLERK';
```
|LAST_NAME|                 HIRE_DATE    | SALARY |  PREV_SAL|
|---|---|---|---|
|Khoo |                     18-MAY-95       |3100   |       0|
|Tobias|                    24-JUL-97 |      2800|       3100|
|Baida |                    24-DEC-97    |   2900     |  2800|
|Himuro          |          15-NOV-98       |2600    |   2900|
|Colmenares      |          10-AUG-99       |2500       |2600|

- we are able to see the previous salary
- using row_number to print first 5 rows
```
WITH   cte
          AS ( SELECT    ROW_NUMBER() OVER (ORDER BY close ) AS ROW_NUM,
            close,
            stk_date                
               FROM     bajaj)
             
    SELECT  *
    FROM    cte
    WHERE   ROW_NUM <= 5
```
- after unifying all the date we create another table bajaj2 to determine if hold/buy/sell

```
CREATE TABLE bajaj2 AS
SELECT stk_date, "Close Price",
(CASE	
	-- First 50 rows keeping Hold because the actual analysis start from 50th row.
	WHEN ROW_NUMBER() OVER(order by stk_date) <=50 THEN 'Hold'
    -- If the difference of short term and long term MA of any day and previous day is positive, then keeping the stocks on Hold
	WHEN ("20 Day MA" - "50 Day MA" > 0 AND LAG("20 Day MA" - "50 Day MA") OVER(order by stk_date) > 0 ) THEN 'Hold'
    -- If the difference of short term and long term MA of any day and previous day is negative, then keeping the stocks on Hold
    WHEN ("20 Day MA" - "50 Day MA" < 0 AND LAG("20 Day MA" - "50 Day MA") OVER(order by stk_date) < 0 ) THEN 'Hold'
    -- If there is no difference of short term and long term MA of any day and previous day, then keeping the stocks on Hold
    WHEN ("20 Day MA" - "50 Day MA" = 0 AND LAG("20 Day MA" - "50 Day MA") OVER(order by stk_date) = 0 ) THEN 'Hold'
    -- If the difference of short term and long term MA of any day is greater than the previous day, then buy the stocks (Golder cross)
    WHEN ("20 Day MA" - "50 Day MA" > 0 AND LAG("20 Day MA" - "50 Day MA") OVER(order by stk_date) < 0 ) THEN 'Buy'
    -- If the difference of short term and long term MA of any day is less than the previous day, then sell the stocks (Death cross)
    WHEN ("20 Day MA" - "50 Day MA" < 0 AND LAG("20 Day MA" - "50 Day MA") OVER(order by stk_date) > 0 ) THEN 'Sell'
END ) Signal
from bajaj1;
```
- similarly create for the remaining tables

- create the bajaj_signal function
```
create or replace function bajaj_signal(
trade_date in Date)
return bajaj2.signal%type
is 
trade_signal bajaj2.signal%type;
begin
SELECT signal into trade_signal 
    FROM bajaj2 
    WHERE stk_date = trade_date ;
return trade_signal;
end bajaj_signal;
```
- calling the above declared function

```
set serveroutput on;
declare
    trade_signal bajaj2.signal%type;
begin
    trade_signal:=bajaj_signal('01-01-15');
    DBMS_OUTPUT.PUT_LINE(trade_signal);
END;
```
- pl/sql for getting all the column names using table name
```
set serveroutput on;
begin
for e in (SELECT  column_name FROM   all_tab_columns WHERE  table_name = 'MASTER_STOCKS' ORDER BY table_name) loop
dbms_output.put_line('column name : '||e.column_name);
end loop;
end;
```
- creating a procedure for the same

```
create or replace procedure get_columns(input_table in varchar2)
is 
begin
for e in (SELECT  column_name FROM   all_tab_columns WHERE  table_name = input_table ORDER BY table_name) loop
dbms_output.put_line('column name : '||e.column_name);
end loop;
end get_columns;

set serveroutput on;
begin
    get_columns('SECTION'); -- just make sure to put the table names in capital
end;
```

- procedure to pass table name as a parameter and getting the count of rows dynamixally
```
create or replace function get_table_count (table_name IN varchar2)
  return number
is
  table_count number;
begin
  execute immediate 'select count(*) from ' || table_name into table_count;
  dbms_output.put_line(table_count);
  return table_count;
end;
set serveroutput on;
declare
    v_func_op number;
begin
    v_func_op:=get_table_count('bajaj');
    DBMS_OUTPUT.PUT_LINE(V_FUNC_OP);
END;
```
- function to insert data
```
create or replace function insert_data (table_name IN varchar2)
  return number
is
  table_count number;
begin

    execute immediate 'insert into stock_report values (''max_close'',2,2,2,2,2,2,2)';

  return table_count;
end insert_data;
set serveroutput on;
declare
    v_func_op number;
begin
    v_func_op:=insert_data('bajaj');
    DBMS_OUTPUT.PUT_LINE(V_FUNC_OP);
END;
```
- function to insert data into stock table
```
create or replace function insert_data (table_name IN varchar2)
  return number
is
    max_close number;
min_close number;
avg_close number(10,2);
stddev_close number(10,2);
golden_cross_buy number;
death_cross_sell number;
total_transactions number;
  table_count number;
  sql_stmt varchar2(1000);
  sss varchar2(1000);
begin
        execute immediate 'select max(close) from '||table_name into max_close;
        execute immediate 'select min(close) from '||table_name into min_close;
        execute immediate 'select avg(close) from '||table_name into avg_close;
        execute immediate 'select stddev(close) from '||table_name into stddev_close;
        sss:='select count(signal) from '||table_name||'2 where signal=''Sell''';
        execute immediate sss into death_cross_sell;
        sss:='select count(signal) from '||table_name||'2 where signal=''Buy''';
        execute immediate sss into golden_cross_buy;
        sql_stmt := 'INSERT INTO stock_report VALUES (:1, :2, :3,:4,:5, :6, :7,:8)';
        EXECUTE IMMEDIATE sql_stmt USING table_name, max_close, min_close,avg_close,stddev_close,golden_cross_buy,death_cross_sell,golden_cross_buy+death_cross_sell;

  return table_count;
end insert_data;

```
- calling the above declared function
```
set serveroutput on;
declare
    v_func_op number;

begin
    v_func_op:=insert_data('hero');

    DBMS_OUTPUT.PUT_LINE('inserted successfully');
END;
```
- execute immediate reference 
https://docs.oracle.com/cd/B13789_01/appdev.101/b10807/13_elems017.htm

- refer this for for-each loop execution with strings
https://tech.nvasilev.com/2013/08/23/english-iterating-over-list-of-values-in-oracle-plsql/

- two different ways to iterate over a list
```
DECLARE
  TYPE STR_LIST_TYPE IS TABLE OF VARCHAR2(15);
  V_STR_VALUES STR_LIST_TYPE;
  V_STR_VALUE VARCHAR2(15);
BEGIN
  V_STR_VALUES := STR_LIST_TYPE('String 1','String 2','gokul');
  FOR INDX IN V_STR_VALUES.FIRST..V_STR_VALUES.LAST
  LOOP
    V_STR_VALUE := V_STR_VALUES(INDX);
     dbms_output.put_line(V_STR_VALUE);
  END LOOP;     
END; 

declare
my_array sys.dbms_debug_vc2coll:= sys.dbms_debug_vc2coll('The', 'Quick', 'brown', 'fox');
begin
  for r in my_array.first..my_array.last
        loop
            dbms_output.put_line( my_array(r) );
       end loop;
    end;
    
```
- populating the stock_report by calling the function inside a loop
```
declare
v_func_op number;
my_array sys.dbms_debug_vc2coll:= sys.dbms_debug_vc2coll('hero', 'bajaj', 'eicher', 'infosys','tvs','tcs');
begin
  for r in my_array.first..my_array.last
        loop
            v_func_op:=insert_data(my_array(r));
            dbms_output.put_line( my_array(r) );
       end loop;
       commit;
    end;
``` 