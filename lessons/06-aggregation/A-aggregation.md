---
description: ""
---

Occasionally you need to query for macro statistics about your tables, not just query for individual rows.

Let's use that we've already used before, `COUNT`. What if we want to know how many ingredients we have overall in our ingredients table?

```sql
SELECT COUNT(*) FROM ingredients;
```

`COUNT` is an aggregation function. We give the `*` is saying "count everything and don't remove nulls or duplicates of any variety".

What if we wanted to count how many distinct `type`s of ingredients we have in the ingredients table?

```sql
SELECT COUNT(DISTINCT type) FROM ingredients;
```

This is going to tell how many different `type`s we have in the ingredients table. Keep in mind the query to see _what_ the distinct ingredients is

```sql
SELECT DISTINCT type FROM ingredients;
```

The first query gives you the number, the count of many distinct things are in the list. The second query gives you what those distinct things are with no indication of how many of each there are. There could be 1 fruit and 10,000 vegetables and you'd have no indication of that.

Okay, so you want to see both at the same time? Let's see that.

```sql
SELECT
  type, COUNT(type)
FROM
  ingredients
GROUP BY
  type;
```

This is combining both of what we saw plus a new thing, `GROUP BY`. This allows us to specify what things we want to aggregate together: the type. Keep in mind if you want to SELECT for something with a GROUP BY clause, you do need to put them in the GROUP BY clause.

> The following query intentionally doesn't work

```sql
SELECT
  title, type, COUNT(type)
FROM
  ingredients
GROUP BY
  type;
```

This query doesn't work because title isn't in the GROUP BY clause. If you do add it, then you're saying "group by unique title + type combinations" which in this case everything is unique. I call this out because people don't realize in order to use SELECT and GROUP BY together, you have to put everything you want to SELECT in the GROUP BY.

## HAVING

Okay, so now you want to select only INGREDIENTS with less than 10 items in your database so you can know what sorts of things you need to add to your database.

The temptation here would be to use `WHERE`

> The following query intentionally doesn't work

```sql
SELECT
  type, COUNT(type)
FROM
  ingredients
WHERE
  COUNT(count) <= 10
GROUP BY
  type;
```

You'll get the error that `count` doesn't exist and it's because `count` isn't something you're selecting for, it's something you're aggregating. The `where` clause filters on the rows you're selecting which happens _before_ the aggregation. This can be useful because let's say we wanted to select only things that have an `id` higher than 30.

```sql
SELECT
  type, COUNT(type)
FROM
  ingredients
WHERE
  id > 30
GROUP BY
  type;
```

Okay, so how we do filter based on the aggregates and not on the rows themselves? With `HAVING`.

```sql
SELECT
  type, COUNT(type)
FROM
  ingredients
GROUP BY
  type
HAVING
  COUNT(type) < 10;
```

And keep in mind you can use both together

```sql
SELECT
  type, COUNT(type)
FROM
  ingredients
WHERE
  id > 30
GROUP BY
  type
HAVING
  COUNT(type) < 10;
```

There are more aggregation functions like `MIN` (give the smallest value in this selected set), `MAX` (same but max), and `AVG` (give me the average). We'll use those in the next exercise with the movie data set.
