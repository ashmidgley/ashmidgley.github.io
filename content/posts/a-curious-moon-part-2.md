---
title: "A Curious Moon - Part 2"
date: "2019-08-28T00:00:00.000Z"
draft: false
---

More database stuff. Buckle up. Little hand says it's time to rock and roll.

![Ex-president](/assets/images/point-break-2.jpg)

#### Views
A stored result set from a query that, once created, you can query just like a normal table. It is a virtual table that is populated dynamically when it is requested by a query.

> "A view is just a projection of data from a source. Nothing is stored anywhere, and every time I query a view I’m actually querying the underlying tables through it."

```
create view example_view as
select
 ...
from example_table
```

#### Materialized views
Same as a view except rather than a virtual table we cache/materialize the resulting data from the query to disk.

```
create materialized view example_view as
select
 ...
from example_table
```

#### Temp tables
Act the same as normal tables except they are deleted when the current client session terminates. Useful for keeping temporary data.

```
create temp table example (
 ...
)
```

#### Junction tables
Used for tables with a many-to-many relationship. Sometimes called link or bridge tables.

Example: Student's and classrooms in a school. 

In this scenario you cannot add foreign keys as a student has multiple classrooms aswell as a classroom having multiple students.

Creating a junction table for tables A and B:
 - Create a table with 2 fields. The primary key of table A and the primary key of table B
 - Then add a primary key for the table which should combine the two foreign keys in order to ensure a unique value.
 
```
create table junction_table (
 table_a_id int not null unique references table_a(id),
 table_b_id int not null references table_b(id),
 primary key(table_a_id, table_b_id)
);
```
 
#### CTE's
Stands for Common Table Expressions. They allow you to chain queries and pass the values into the next query block. It's like functional SQL. Used across the board in different RDBMS's.

```
with first as(
 select
  ...
 from example_table
), second as(
 select
  ...
 from first
)
select * from second;
```
 
#### Functions
Like a fuction elsewhere in programming except it's applied to a database and is written in SQL.

```
drop function if exists match_time (
 double precision,
 double precision
);

create function match_time (
 yr double precision,
 wk double precision,
 out timestamp
)
as $$
 select
  timestamp
 from example_a_table
 where year = yr and week = wk
$$ language sql;
```

Then when we want to call the function:
```
select 
 match_time(year, week) as time_stamp,
from example_b_table
```
 
#### Aggregate functions
A type of function where values of multiple rows are grouped together to form a single value.

Common examples of grouping using aggregate functions:
- Average
- Count
- Min
- Max
- Median
- Mode
- Range
- Sum

```
select 
 count(1) as count, 
 description
from events
group by description;
```

#### Window functions
Work in the same fashion as aggregate function's but instead of reducing multiple rows into a single row, they keep all rows while still applying the grouping on the value you have specified.

```
select 
 description,
 count(1) over (partition by description)
from events
```

In the above example the partition keyword is used and you would get multiple descriptions with the count rather than just a single description with the count like in an aggregate function. 

Example output:
```
description | count
-------------------
value1      | 3
value1      | 3
value1      | 3
value2      | 7
value2      | 7
value3      | 4
```

Aggregate function output:
```
description | count
-------------------
value1      | 3
value2      | 7
value3      | 4
```

This example seems useless but there are cases where window functions are useful. To see a case of a window function being put to good use, refer to page 230 in the book.
 
#### Sargeable vs. non-sargeable queries
Sargeable:
 - Search ARGumEnt ABLE 
 - Query that can be optimized by using indexes for faster searches and execution.
 - Good -> try to always use.
 
> "Sometimes you can’t get away from searching with a fuzzy string parameter, which is OK. This is a database after all. The trick is to be sure that you’re using some kind of optimization. Otherwise, Postgres has a lot of work to do."

Non-sargeable:
 - Query that can't be optimized.
 - To find the rows your looking for, postgres has to search every row in the table (sequential scan).
 - AVOID.
 
> "It's not a big deal if the database is small, but as it grows the search will begin to slow down. Over time, maybe days, maybe years, the table will grow to the point that searches are too slow to run."

#### Full-text indexing
Indexing a body of text for faster querying.

> "You basically tweak a body of text and index it, prioritizing useful terms and deprioritizing 'noise.' If you do it right, you can make finding things a lot easier for Postgres. This is how Google makes money."

```
create view example_view as
select
 description
 to_tsvector(description) as search
from example_table
```

Then when you are searching the view:
```
select 
 id, date, title
from example_view
where search @@ to_tsquery('testing');
```

@@ = output matches. Other search symbols [here](https://www.postgresql.org/docs/current/functions-textsearch.html). More usages of to_tsvector [here](https://www.postgresql.org/docs/current/textsearch-controls.html).

#### Ranges
Postgres allows datatypes that are ranges. There are numeric and date/time ranges.

```
alter table example_table
add range_test tsrange;

update example_table
set range_test = tsrange(start, end, '[]');
```

> "The [] option tells Postgres that this range is inclusive, meaning the upper and lower bounds of the range should be included. If I had used () as an option the range would be exclusive, meaning all dates between the upper and lower bound are considered the value. You can combine these two symbols as well. For instance, this: [) is a range that is inclusive on the lower bound, exclusive on the upper."

Querying a range:
```
select 
 description 
from example_table
where range_test @> '2019-01-15'::timestamp;
```

#### Analyzing query execution
When tossing up between queries you can use the `explain` keyword (before your query) and postgres will give you a break down of what happens on execution.

Cost:
 - Relative measurement of time it takes to read data from disk.
 - Lower = better.

Rows:
 - Estimated number of rows output from the query.
 
```
explain update example_table
set example_id = (
    select id from another_table
    where date = example_table.time_stamp::date
    limit 1
);
```

You can also use `explain analyze` and this will run the query and tell you what happened.

#### End

Sheesh, my head hurts. Congrats on making it this far. If you're interested in learning more, grab a copy of the book at [Big Machine](https://bigmachine.io/products/a-curious-moon) or check out the database related posts on [Rob's blog](https://rob.conery.io/categories#Database).