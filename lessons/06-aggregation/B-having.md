---
title: "HAVING"
---

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
