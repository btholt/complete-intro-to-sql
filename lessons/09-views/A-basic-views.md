---
description: ""
---

In the previous lesson we saw a way to make a partial index on the category_names table on only the English category names. What if we could make a mini-table of just those category names so we didn't have to add a `WHERE language='en'` on every single query we do? This what views are: they're lens we can query through that we can treat as if they were just normal tables.

```sql
CREATE VIEW
  english_category_names
AS
SELECT
  category_id, name
FROM
  category_names
WHERE
  language='en';
```

Now you can do:

```sql
SELECT * FROM english_category_names LIMIT 5;
```

A few things to note here:

- We're querying this view as if it was a normal table. That's the power of views. You get to treat them as normal tables in your querying.
- Imagine you have a contractor working on your app with you but you don't want to give them access to everything in a table that they need to work on. You can make a view and give them only access to that one view. One of the powerful aspects of views.
- Views can utilized underlying indexes. You can't index a view itself but the data itself can be indexed.

## Inserting into views

TODO

## Views with joins

This is cool to have a filtered view on a table, but let's make it even more compelling. A view can be more-or-less any SELECT query. So we can put joins together so instead of wild joins we can just query a view.

Let's say you have a page on your app that you need to display actors & actresses, the roles they played, and the movies those roles were in and this was a really common querying pattern for your app. Your query would look like:

```sql
SELECT
  p.name AS person_name, c.role, m.name AS movie_name, p.id AS person_id, m.id AS movie_id
FROM
  people p

INNER JOIN
  casts c
ON
  p.id = c.person_id

INNER JOIN
  movies m
ON
  c.movie_id = m.id

WHERE
  c.role <> ''
LIMIT 5;
```

It's a bit of a mouthful from a quuerying perspective. So let's make it a view!

```sql
CREATE VIEW
  actors_roles_movies
AS
SELECT
  p.name AS person_name, c.role, m.name AS movie_name, p.id AS person_id, m.id AS movie_id
FROM
  people p

INNER JOIN
  casts c
ON
  p.id = c.person_id

INNER JOIN
  movies m
ON
  c.movie_id = m.id

WHERE
  c.role <> '';
```

Now you can do

```sql
SELECT * FROM actors_roles_movies LIMIT 20;
```

Much easier, right? And now we can do queries with this too! We can treat these views as if they were real tables when we do joins.

What actors & actresses tend to act in the same sorts of movies? We can know that by joining category names to movie keywords to movies to casts to people, right? Well, here we can make use of both of our views because we already have people to movies connected, and we have another view with just English category names already, so let's use both!

> This is a very expensive query (most expensive so far, I got `cost=293871.35..293871.40`.) If you have a slow computer, uncomment the WHERE clause.

```sql
SELECT
  arm.person_name, ecn.name AS keyword, COUNT(*) as count
FROM
  actors_roles_movies arm

INNER JOIN
  movie_keywords mk
ON
  mk.movie_id = arm.movie_id

INNER JOIN
  english_category_names ecn
ON
  ecn.category_id = mk.category_id

-- WHERE arm.person_name = 'Julia Roberts'

GROUP BY
  arm.person_name, ecn.name

ORDER BY
  count DESC

LIMIT 20;
```

This is the best part about views! We get the full power of PostgreSQL behind this summarized views that live-derive from our existing data.
