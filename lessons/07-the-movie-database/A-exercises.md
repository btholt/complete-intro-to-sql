```sql
SELECT
  m.name
FROM
  movies m

INNER JOIN
  cast c
ON
  m.id = c.movie_id

INNER JOIN
  people p
ON
  c.person_id = p.id

WHERE
  p.name = 'Keanu Reeves';
```
