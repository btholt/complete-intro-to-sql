Some times you need two queries to do one thing. Some times you can rethink queries to accomplish the same thing but other times it's easier to just have two queries. You could do this in code: query and id and then prepare a second query using the result of the first to get what you're looking for, but it'd be so much easier if we could embed that. And you guessed it, you sure can.

Let's see how. What if we I wanted to find everyone that was cast in `Tron Legacy` and we don't know the id off the top of our heads? We could absolutely do that with joins but we can actually do it with a subquery too.

```sql
SELECT
  p.name
FROM
  casts c

INNER JOIN
  people p
ON
  c.person_id = p.id

WHERE
  c.movie_id = (
    SELECT
      id
    FROM
      movies
    WHERE
      name = 'Tron Legacy'
  );
```

- The `()` represent that you can doing to do a subquery. This one will run first and then its results will be used in the second query.
- A subquery must return a single column. I think that makes sense here since we're asking for the id from the movies table.
- On one hand nesting makes thing hard to read. On the other hand grasping joins at a glance be a lot of cognitive load. Use your best judgment on which is more "readable" to you. Sometimes it'll be a subquery, sometimes it'll be a join.

To see how to do this with a join, try this query:

```sql
SELECT
  p.name
FROM
  casts c

INNER JOIN
  people p
ON
  c.person_id = p.id

INNER JOIN
  movies m
ON
  c.movie_id = m.id
AND
  m.name='Tron Legacy';
```

So which one is "better"? That's really up to you. The cost of the subquery one is `cost=156.73..308.04` and the cost of the inner join one is `cost=148.86..175.03`. That's mostly negligible in a production environment but the join is faster this time. 99% of the time I'd say joins will be faster but frequently it doesn't much matter. When it's close enough, choose which one you can maintain better (generally speaking, optimize queries that are slow and run frequently.)
