## Which movie made the most money?

```sql
SELECT
  name, revenue
FROM
  movies
ORDER BY
  COALESCE(revenue, 0) DESC
LIMIT 1;
```

## How much revenue did the movies Keanu Reeves act in make?

```sql
SELECT
  COALESCE(SUM(m.revenue),0) AS total
FROM
  movies m

INNER JOIN
  casts c
ON
  m.id = c.movie_id

INNER JOIN
  people p
ON
  c.person_id = p.id

WHERE
  p.name = 'Keanu Reeves';
```

## Which 5 actors were in the movies that had the most revenue?

```sql
SELECT
  p.name, COALESCE(SUM(m.revenue),0) AS total
FROM
  movies m

INNER JOIN
  casts c
ON
  m.id = c.movie_id

INNER JOIN
  people p
ON
  c.person_id = p.id

GROUP BY
  p.id, p.name

ORDER BY
  total DESC

LIMIT 5;
```

## Which 10 movies have the most categories?

```sql
SELECT
  m.name, count(c.id) AS count
FROM
  movies m

INNER JOIN
  movie_keywords mk
ON
  mk.movie_id = m.id

INNER JOIN
  categories c
ON
  mk.category_id = c.id

GROUP BY
  m.id, m.name

ORDER BY
  count DESC

LIMIT 10;
```

## Which category is associated with the most movies

```sql

```
