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
- piece by piece
```
select trunc(quote_date, 'y') y,
           1200 / min(low) shares, -> this is the shares that can be bought with 1200 on minimum low day on the year
           1200 purchase
    from   sp_quotes 
    group  by trunc(quote_date, 'y')
```
- final part understanding
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
                 ((( sum(shares)*max(close) )- sum(purchase) )/ sum(purchase) ) * 100,  -- (total selling price - total buying price/total buying)*100 -> percentage(pct) gain
                 2
                ) pct_gain,
           to_char(sum(shares)*max(close), 'fm$999,999.00') value,
           sum(purchase) total_spend
    from   share_purchases, final_close;
```
- Unfortunately, we can only know the cheapest price in a year once it's over. By this time it's too late to buy shares at that price!
 
- These two strategies give us reference points. Now let's a take a look at simple passive investing methods we can actually follow. First, buying $100 at the start of each month. Using this approach, we'll make our purchase when the market opens on the first trading day of each month.
 
- To find the first day of each month we can use trunc again. 
    - If we pass 'mm' instead of 'y', it converts dates to the first of the corresponding month
    - So to return the quotes on these days, join the quote date to the result of month truncing, like so:

    ```
    where quote_date = trunc(quote_date, 'mm')
    ```
- Again, we need to calculate how many shares we buy. This is 100 divided by the opening price. To confirm how much we're spending, I've also included a purchase column fixed at 100. This gives the following query.
```
select quote_date, 
         100 / open shares,
         100 purchase
  from   sp_quotes 
  where  quote_date = trunc(quote_date, 'mm')
```
- At this point we can realize a benefit of using the with clause. The query to calculate returns for monthly purchases is similar to the one for yearly buying. We just need to change how many shares we are buying when. To do this, we just replace share_purchases with the query above.
 
- To validate our expenditure, I've also add the sum of the purchase column. This gives:

```
with final_close as (
    select close
    from   sp_quotes
    where quote_date = (select max(quote_date) from sp_quotes) --> final close on last date
  ), share_purchases as (
    select quote_date, 
           100 / open shares,
           100 purchase
    from   sp_quotes 
    where  quote_date = trunc(quote_date, 'mm') --> creating  a table with date, and opening price on 1st day of month
  )
    select round(
                 ((( sum(shares)*max(close) )- sum(purchase) )/ sum(purchase) ) * 100, 
                 2
                ) pct_gain,
           to_char(sum(shares)*max(close), 'fm$999,999.00') value,
           sum(purchase) total_spend
    from   share_purchases, final_close;
```
- ## ALITER
    - create views and then call the last part.
    - the above code generates views on the fly as said at the start.
    ```
    create view final_close as
    select close
    from   sp_quotes
    where quote_date = (select max(quote_date) from sp_quotes);
    ```
- create a separate view for share_purchases
```
    create view  share_purchases as (
    select quote_date, 
           100 / open shares,
           100 purchase
    from   sp_quotes 
    where  quote_date = trunc(quote_date, 'mm')); --> creating  a table with date, and opening price on 1st day of month;
    
    select * from share_purchases;
```
- this query gives us the number of entries every month
```
Select count(1), to_char(quote_date, 'YYYY-MM') AS yyyymm
from sp_quotes 
group by  to_char(quote_date, 'YYYY-MM')
order by to_char(quote_date, 'YYYY-MM');
```
- getting the start date in each month
```
select min(quote_date) from sp_quotes
group by  to_char(quote_date, 'YYYY-MM')
order by to_char(quote_date, 'YYYY-MM');
```
- create a view with start dates of a month
```
create view starting_day_of_month as
select min(quote_date) start_date from sp_quotes
group by  to_char(quote_date, 'YYYY-MM')
order by to_char(quote_date, 'YYYY-MM');
```
- the above share_purchases view as in the blog is not working because it considers only the months on which which has 1 as the starting date.
the modified view code is as  below
```
    create or replace view share_purchases as
    select quote_date, 
           100 / open shares,
           100 purchase
    from   sp_quotes 
    where  quote_date in (select * from starting_day_of_month ) order by quote_date;
```
- now this will give the required output
```
    select round(
                 ((( sum(shares)*max(close) )- sum(purchase) )/ sum(purchase) ) * 100, 
                 2
                ) pct_gain,
           to_char(sum(shares)*max(close), 'fm$999,999.00') value,
           sum(purchase) total_spend
    from   share_purchases, final_close;
```
- the combined one where the expenses is 12000
```
with final_close as (
    select close
    from   sp_quotes
    where quote_date = (select max(quote_date) from sp_quotes)
  ), share_purchases as (
    select quote_date, 
           100 / open shares,
           100 purchase
    from   sp_quotes 
    where  quote_date in (select min(quote_date) 
                          from   sp_quotes 
                          group  by trunc(quote_date, 'mm'))
  )
    select round(
                 ((( sum(shares)*max(close) )- sum(purchase) )/ sum(purchase) ) * 100, 
                 2
                ) pct_gain,
           to_char(sum(shares)*max(close), 'fm$999,999.00') value,
           sum(purchase) total_spend
    from   share_purchases, final_close;

```
- Now we've spent our whole budget. Our gains are notably worse than both methods we described previously however. It's ten percentage points worse than buy-and-hold (69.89) and thirty worse than timing the market (90.93).
 
- Are there any other simple, regular investment approaches we could try?
 
- We can't know at the time if the price on a given day is the lowest the market will be that week, month or year. We can identify local minimums though. If the market closes lower than it opens it's a daily low. If this fall is great enough it may also be a weekly or even monthly low.
 
- What if we create a rule to buy the day after the index falls 2% or more?
 
- Sticking with the total return formula, we can identify these days using the following equation:
```
((close-open)/open)  < -0.02
```
- First, a sanity check for how viable this approach is. We've assumed 120 rounds of $100 purchases. So we should check how often there is a two percent drop in a day. If there's significantly more than 120, we can't afford it. Notably less and we're leaving too much money on the table.
 
 - To find the number of days, just count how many rows meet the formula:
    ```
    select count(*)
    from   sp_quotes
    where  ((close-open)/open)  < -0.02;
    ```
- To find the daily fall, we have to wait until the market closes. Therefore we'll make our purchases the day after the S&P drops. This means we need to find the opening price for the next day (tomorrow's opening can be different from today's close).
 
- We can use analytic functions to do this. Lead() enables us to fetch values from the next row in the results. We pass this the column we want values for. We want the opening price, so we're looking for lead(open). Next we need to tell Oracle which order to traverse the results. We do this with an over() clause. This takes an order by clause including the column we want to sort our data by. Here this is quote_date, giving:
    ```
    lead(open) over (order by quote_date)
    ```

- here is the list of analytical functions
    - https://oracle-base.com/articles/misc/analytic-functions

- We can then add this to our select statement.
 
- It's tempting to replace count(*) in our earlier query with this function and the other values we want like so:
```
select quote_date, 
         lead(open) over (order by quote_date) purchase_price, 
         100/lead(open) over (order by quote_date) shares, 
         (close-open)/open day_return,
         100 purchase
  from   sp_quotes
  where  (close-open)/open < -0.02
```
- with comments 
```
select quote_date, 
         lead(open) over (order by quote_date) purchase_price,  -- basically the next days price
         100/lead(open) over (order by quote_date) shares, -- no of shares that can be bought with 100$ with next days opening price
         (close-open)/open day_return, -- this is basically the % decrease in the day return, just to verify manually that it is less than -0.02%
         100 purchase -- thi is the amount spent on purchasing
  from   sp_quotes
  where  (close-open)/open < -0.02;
```
- the above query returns the purchase_price for the 109 days where the return return is less than -2%.When means the prices are dropping hence buy the stock.

- But what is this lead ? Try this.
```
select open,lead(open) over (order by quote_date) from sp_quotes;
```
- so if we see lead operator gives us the next value to us.i.e it say what is the value that is lead by the current value.in simple words here, it is the next day opening price , ie the purchase price.
- remember we want to see where the price drops below 2% and buy it the next day when market opens.
- This gives incorrect results however.
 
- The where clause is processed before analytics. So in the query above, lead() only applies to rows where there's a 2% or more fall instead of all the rows. This means lead returns the opening price on the next day there's a big drop, not the day immediately after a fall!
 
- This isn't what we want. We need the opening price on the next trading day. So we need to place the query above inside an inline view. The where clause goes outside this view:
```
select * from (
    select quote_date, 
           lead(open) over (order by quote_date) purchase_price, 
           100/lead(open) over (order by quote_date) shares, 
           (close-open)/open day_return,
           100 purchase
    from   sp_quotes
  )
  where  day_return < -0.02;
```
- with comment for inner query.
```
select quote_date, open,
           lead(open) over (order by quote_date) purchase_price,  -- price of next day
           100/lead(open) over (order by quote_date) shares,  -- no of shares to be bought with next day opening price
           (close-open)/open day_return, -- day return of today
           100 purchase
    from   sp_quotes order by quote_date;
```
- what this code does is that it calculates return for each day, 
now lets compare both the queries

|old|new|
|---|---|
|
```
select quote_date,
         lead(open) over (order by quote_date) purchase_price, 
         100/lead(open) over (order by quote_date) shares, 
         (close-open)/open day_return,
         100 purchase
  from   sp_quotes
  where  (close-open)/open < -0.02
  ```
  |
  ```
  select * from (
    select quote_date, 
           lead(open) over (order by quote_date) purchase_price, 
           100/lead(open) over (order by quote_date) shares, 
           (close-open)/open day_return,
           100 purchase
    from   sp_quotes
  )
  where  day_return < -0.02;
  ```
  
  |old_explanation|new_explanation|
  |---|---|
  |The where clause is processed before analytics. So in the query above, lead() only applies to rows where there's a 2% or more fall instead of all the rows|We need the opening price on the next trading day. So we need to place the query above inside an inline view. The where clause goes outside in second view:|
  | This means lead returns the opening price on the next day there's a big drop, not the day immediately after a fall!|we can now replace share_purchases in the full query. With this in place it's time to find our gains:|

  - As before, we can now replace share_purchases in the full query. With this in place it's time to find our gains:

```
  with final_close as (
    select close
    from   sp_quotes
    where quote_date = (select max(quote_date) from sp_quotes)
  ), share_purchases as (
    select * from (
    select quote_date, 
           lead(open) over (order by quote_date) purchase_price, 
           100/lead(open) over (order by quote_date) shares, 
           (close-open)/open day_return,
           100 purchase
    from   sp_quotes
  )
  where  day_return < -0.02
  )
    select round(
                 ((( sum(shares)*max(close) )- sum(purchase) )/ sum(purchase) ) * 100, 
                 2
                ) pct_gain,
           to_char(sum(shares)*max(close), 'fm$999,999.00') value,
           sum(purchase) total_spend
    from   share_purchases, final_close;
```

|PCT_GAIN |VALUE |                                   TOTAL_SPEND|
|---|---|---|
|93.26|	$21,065.28|	10900|

- More than 91%! This is the best we've done so far. It works with information we have available and we have $1,100 left over :)
- This looks like a winning strategy. It's easy to do. Potentially we could automate the whole process. There's no guarantee it'll work in the future. Based on this analysis it's tempting to try however.
- Before you rush out to do this we need to analyze this further however. There are big problems with this approach. We'll look at these in the next post

- see this later
https://blogs.oracle.com/authors/chris-saxon