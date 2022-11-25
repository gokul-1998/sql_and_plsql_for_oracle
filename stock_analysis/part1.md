The view a1 is union of all the three tables namely
`airtel,maruti,untracemco`

- this sql query returns a table of average volatility for the stocks
```
select symbol,round(avg(high-low),2) as avg_volatility,dense_rank()
over(order by avg(high-low) asc) ranking from a1
group by symbol;
```
- how to use select statement in pl/sql block

```
set serveroutput on;
DECLARE
  ssymbol a1.symbol%TYPE;
BEGIN

  SELECT symbol INTO ssymbol
  FROM a1
  WHERE high = 1118;

  dbms_output.put_line( ssymbol );
END;
```
- create a procedure to calculate the average volatility of a given stock
```
    create or replace procedure avg_volatility_of_stock(avg_volatility out decimal,
    stock_symbol in a1.symbol%type,
    p_error_msg out varchar2)
    is
    begin
    select round(avg(high-low),2) into avg_volatility from a1 where symbol=stock_symbol;
    dbms_output.put_line('the average volatility of '|| avg_volatility);

    exception
        when others then
            p_error_msg:=sqlerrm;
    
    end avg_volatility_of_stock;
```
- how to call the above declared procedure
```
    declare
    ag decimal(10,2);
    stock_symbol a1.symbol%type:='MARUTI';
    p_error_msg varchar2(12222);
begin
    avg_volatility_of_stock(ag,stock_symbol,p_error_msg);
    dbms_output.put_line('the value is '|| ag);
end;
```