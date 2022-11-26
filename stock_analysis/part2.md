- following the blog in oracle official
- https://blogs.oracle.com/sql/post/calculate-stock-market-returns-using-sql

```
create table sp_quotes (
    quote_date date primary key, 
   open       number(12,6), 
 high       number(12,6), 
   low        number(12,6), 
  close      number(12,6), 
  volume     number(12,0), 
   adj_close  number(12,6)
  );
```
- for this part ,use the `stock data.csv` 
- calculate returns using the total return formula:
```
(End Value - Initial Value) / Initial Value
```

- select maximum closing, minimum opening, and return of that two.
```
select max(close),min(open),round((max(close) - min(open)) / min(open) * 100, 2) from sp_quotes;
```
- select * from both min(open) and max(close)
```
select * from sp_quotes
where close=(select max(close) from sp_quotes);

select * from sp_quotes
where open=(select min(open) from sp_quotes);
```
- this gives the start date and end date
```
select max(quote_date),min(quote_date) from sp_quotes;
```
- 
```
select round((max(close) - min(open)) / min(open) * 100, 2) pct_gain,
         to_char((1 + (max(close) - min(open)) / min(open)) * 12000, 'FM$999,999.00') total -- format specifier, '' understand what this does?? ''
  from   sp_quotes;
```
- Let's look at the returns from a (hypothetical) "timing the market" method.
 
If we're lucky enough to buy shares at their cheapest each year we're "buying low". Let's calculate our gains if we make a single $1,200 purchase each year when the S&P is at its lowest that year.
 
To do this we need to group all the dates within a year together. 
- We can easily do this using the function trunc(<date>, 'y'). This converts the supplied date to the 1 Jan in the same year. 
- To find the cheapest price, group by this function and return the minimum low:
```
select trunc(quote_date, 'y') y, min(low)
  from   sp_quotes
  group  by trunc(quote_date, 'y')
  order  by trunc(quote_date, 'y');
```
- Hey what is this ` trunc ` function? i dont know.
    - dont worry refer this
        - https://www.ibm.com/docs/en/informix-servers/14.10?topic=functions-trunc-function
<br></br>
- Next we need to calculate how many shares this purchases. Do this by dividing $1,200 by each min(low). Summing this up gives our total holding.
 
- To find the final value of our shares, just multiply our total shares by the closing price on the last day.
 
- We can find the final closing price with the following query:
    ```
    select close
  from   sp_quotes
  where  quote_date = (select max(quote_date) from sp_quotes);
  ```
  -- complete code
```
with final_close as (
    select close
    from   sp_quotes
    where quote_date = (select max(quote_date) from sp_quotes)
  ), share_purchases as (
    select trunc(quote_date, 'y') y, 
           1200 / min(low) shares,
           1200 purchase
    from   sp_quotes 
    group  by trunc(quote_date, 'y')
  )
    select round(
                 ((( sum(shares)*max(close) )- sum(purchase) )/ sum(purchase) ) * 100, 
                 2
                ) pct_gain,
           to_char(sum(shares)*max(close), 'fm$999,999.00') value,
           sum(purchase) total_spend
    from   share_purchases, final_close;

```




