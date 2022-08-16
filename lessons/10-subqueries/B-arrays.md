You _can_ return multiple values from a subquery but you have to handle the results correctly.

Let's say you wanted to see all the keywords associated with any _Star Wars_ movie. How would you go about doing that? You could do joins and have a row per keyword but that's pretty annoying and you'd need code to unravel that mess and aggregate it likely. A subquery with an ARRAY constructor would do this much better.

```sql
SELECT
  m.name,
  ARRAY(
    SELECT
      ecn.name
    FROM
      english_category_names ecn
    INNER JOIN
      movie_keywords mk
    ON
      mk.category_id = ecn.category_id
    WHERE
      m.id = mk.movie_id
    LIMIT 5
  ) AS keywords
FROM
  movies m
WHERE
  name ILIKE '%star wars%';
```

- Array is a datatype in PostgreSQL. Here we're using that to aggregate our answers into one response.
- Notice we can use `m.id` in our subquery despite the fact the subquery doesn't reference movies at all. This is possible to do. The subquery has the context of the query it's inside. Makes it so we don't have to do that join inside of the subquery.
- These kinds of queries can get very expensive very quickly. We essentially made a loop with SQL where every row gets its own SELECT subquery. If the outter query returns a lot of rows and the inner query is expensive this will get out of hand quickly.
