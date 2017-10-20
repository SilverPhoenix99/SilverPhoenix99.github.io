---
title:  "A DB2 SQL secret you wish you knew earlier"
date:   2009-06-10 21:16:00 GMT
tags:   as400 db2 sql
---
I don't know how many people know about this, but I found a neat trick when using DB2 SQL.

Usually you have to query something like this:

```sql
select
    *
from
    customer
where
        last_name = 'SMITH'
    and age = 32
```

In DB2 SQL (at least it works in iSeries/AS400), you can simplify it like this:

```sql
select
    *
from
    customer
where
    (last_name, age) = ('SMITH', 32)
```

This is the tuple syntax.

Maybe it doesn't seem very useful, but most of the tables that I work with have a composite primary key made of 2 or 3 fields (e.g., a product id field and a unique serial number per product).

The real advantage comes when using a group of rows. For instance, when you have multiple conditions:

```sql
select
    *
from
    customer
where
       (last_name = 'SMITH' and age = 32)
    or (last_name = 'JONHSON' and age = 45)
```

With tuples, it's much simpler to write it:

```sql
select
    *
from
    customer
where
    (last_name, age) in (values
        ('SMITH', 32),
        ('JOHNSON', 45)
    )
```
