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
- for this part ,use the `stock data csv` 
- calculate returns using the total return formula:
