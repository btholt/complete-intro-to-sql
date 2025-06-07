---
description: ""
---

Let's next hop into SELECT, or how to read data from a database. We've already seen the most basic one.

```sql
SELECT * FROM ingredients;
```

The \* there represents all available columns. Frequently, for a variety of reasons, we do not want to select everything. In general, I would recommend only using \* where your intent is truly "I want _anything this database could ever store for these records_". Frequently that is not the case. Frequently it is "I need the name, address and email for this email, but not their social security or credit card number". This is positive because it means smaller transfer loads, that your system only processes data it needs and not the rest, and it shows intention in your code. Honestly the biggest benefit is the latter: to show intentions in your code.

So in our case we could put

```sql
SELECT id, title, image, type FROM ingredients;
```

When you read this now you know that the former coder was actively looking for those three columns.

## LIMIT and OFFSET

Okay, now what if the user only wants five records?

```sql
SELECT id, title, image
FROM ingredients
LIMIT 5;
```

This will limit your return to only five records, the first five it finds. You frequently will need to do this as well (as in almost always) since a database can contain _millions_ if not _billions_ of records.

So what if you want the next five records?

```sql
SELECT id, title, image
FROM ingredients
LIMIT 5
OFFSET 5;
```

This can be inefficient at large scales and without the use of indexes. It also has the problem of if you're paging through data and someone inserts a record in the meantime you could shift your results. We'll get into optimizing queries a bit later, but just be aware of that.

In our exercise that is upcoming, feel free to use OFFSET.

## WHERE

Sometimes you don't want all of the records all at once. In that case you can add a WHERE clause where you tell the database to filter your results down to a subset of all of your records. What if we only wanted to show only fruits?

```sql
SELECT *
FROM ingredients
WHERE type = 'fruit';
```

This will give us all the fruits we had. What if we wanted to only select vegetables where the IDs are less 20?

```sql
SELECT *
FROM ingredients
WHERE type = 'vegetable'
  AND id < 20;
```

AND allows you to add multiple clauses to your selects. As you may guess, the presence of AND belies the existance of OR

```sql
SELECT *
FROM ingredients
WHERE id <= 10
  OR id >= 20;
```

## ORDER BY

You frequently will care about the order these things come back in, how they're sorted. That's what ORDER BY is for.

Let's say you wanted to order not by id or insertion order but by title.

```sql
SELECT * FROM ingredients ORDER BY title;
```

This will alphabetize your returned list. What if we wanted it in reverse order of IDs?

```sql
SELECT * FROM ingredients ORDER BY id DESC;
```

This will start at the largest number and count backwards. As you may have guessed, `ASC` is implied if you don't specify.
