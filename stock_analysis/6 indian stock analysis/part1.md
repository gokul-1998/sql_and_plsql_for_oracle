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



