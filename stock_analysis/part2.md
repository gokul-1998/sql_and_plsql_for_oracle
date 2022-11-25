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










