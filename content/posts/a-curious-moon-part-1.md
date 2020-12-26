---
title: "A Curious Moon - Part 1"
date: "2019-08-21T00:00:00.000Z"
draft: false
---

I remember my database paper at uni. It was cool. 1pm lectures, Small data sets and clean input data. "I can't wait to start working with some real data in a big application" I thought to myself as I walked away from Owheo lab for the last time.

Fast forward to my first dev job:

"Any idea why SQL Server is down?" 

SQL what? 

"Just use an aggregate function to get the count of x"

Huh? 

"Be a doll and write us a window function to show x, y and z"

*Slowly climbs under desk and gets into fetal position.*

I had a bad case of Databaseitis. I needed a remedy. So I read [A Curious Moon](https://bigmachine.io/products/a-curious-moon) by Rob Conery. Below is a brain dump of things I wrote at the time. It contains a bunch of excerpts from the book.

Let's dive on in.

![Skydive](/assets/images/point-break.jpg)

#### SQL
Stands for Structured Query Language. It's a language used to work with data in databases.

#### RDBMS
Relational Database Management system. A kind of system assumes there are relations between the different sets of data (tables). Examples: PostgreSQL, Microsoft SQL Server (MSSQL), MySQL.

#### NoSQL
Database system's that do not have relational data. Increasingly being used in big data and real-time web applications. Examples: MongoDB, Cassandra, Redis.

#### Idempotent script
A script that you can run multiple times and it will always run, with the same result. 

```
drop table if exists example_table
```

If you re-run the query it will remove the previous table created, making the script idempotent.

#### psql
PostgreSQL command line interface.

#### Make
Makefile's define a set of tasks to be executed that generate a target/build. Very handy for automating the execution of multiple scripts. Example from the book [here](https://github.com/red-4/curious-moon/blob/master/01-enceladus_db/Makefile).

#### Joins
Allow us to combine data from 2 different data sets (tables). In SQL, there are 4 basic types of joins:
 - Inner
 - Left
 - Right
 - Full
 
The easiest way for me to visualize different join's is with [venn diagrams](https://www.sql-join.com/sql-join-types/).
 
#### Primary keys
Uniquely identifies each row in a table.

```
add id serial primary key;
```

#### Foreign keys
Table value that references a primary key in another table. This allows us to link/establish a relationship between 2 tables.
```
add example_id int references example_table(id);
```

#### Schemas
Database structures can be grouped into schema's for different uses. Creating schema's can be useful for manipulating data that you don't want in the public schema.

> "I’ve been dumping raw CSV data into my public schema, which, yeah, seems kind of goofy. In programming terms, I suppose this could be like the master branch in Git, the public directory in Rails or the global namespace in client-side JavaScript."

Example: using an import schema before tidying up the data and moving it to the public schema.

```
create schema if not exists import;
create table import.example_table (
 ...
)
```

#### Normalization
Used to reduce repetition in a database so we take up less disk space and can perform operations quicker. There are several forms. The general rule is that a relation is normalized if it meets the third normal form (3NF). Most of the time we create lookup tables to normalize a relation and replace the values in the 'source' table with the primary keys from these lookup tables.

#### Lookup tables
To create a lookup table:
 1. Get all the distinct values for a column in the table.
 2. Create a new table using this data.
 3. Add a primary key for each entry. This value will replace the value in the original 'source' table.

Say we have a source table which holds data for transactions for a food stand:
```
id | customer_id | order
-------------------
1 | 2 | 'Pickle sandwich'
2 | 5 | 'Ham baguette'
3 | 7 | 'Pickle sandwich'
4 | 8 | 'Snag'
5 | 2 | 'Pickle sandwich'
```

We could create a lookup table for orders to remove repeated text values:
```
id | description
-------------------
1 | 'Pickle sandwich'
2 | 'Ham baguette'
3 | 'Snag'
```

And alter the source table to become:
```
id | customer_id | order_id
-------------------
1 | 2 | 1
2 | 5 | 2
3 | 7 | 1
4 | 8 | 3
5 | 2 | 1
```

#### ETL
Stands for extract, transform and load. It's a process used when we want to take data from another system and load it into our database. 

> "ETL is about pulling, prepping and loading a ton of data. It's mind numbing, tedious, and incredibly rewarding one you get to play with the data that you've loaded." 

#### Extraction
Pretty straight forward compared to transformation and load. It's just the process of pulling relevant data out of the various systems.

#### Transformation
Cleaning up the extracted data so it's ready to be dumped into our database. This involves handling bogus values or data that isn't in the correct format.

You need to make sure of 3 things:
 - We have correct typing. Example: seeing '-' in place of a number.
 - Completeness. Data may need to be thrown out because it is incomplete or because we can't handle the typing.
 - Accuracy. Example: say we have a column for values in degrees. Any values below 0 or over 360 would be incorrect. 

> "This is the most involved part of the ETL process. The last thing we want to do is to make data incorrect while trying to correct it. Our goal is correct, complete, accurate data"

#### Load
Pushing the transformed data into our normalized database tables. Typically we can use shell scripts and Make files for this. With large data sets you may need to use other tools but I haven't explored these.

> "Import everything as text. Add types later on. You want the data in the database first, everything else can wait."

```
COPY import.example_table 
FROM '/path/to/csv/.../example.csv' 
WITH DELIMITER ',' HEADER CSV;
```

#### Index
A type of data structure that helps us retrieve data faster. They mean we don't have to search every row in a table when that table is accessed. It comes with the cost of extra writes and storage space.

> "I do know about indexes. They work just like their analog that you would find in the back of a book. You find the term you’re looking for by using an alphabetical lookup and then head to a given page. Much faster than scanning every single page for a term, no?"

```
create index idx_example_search
on example_table using GIN(search);
```

To be continued...