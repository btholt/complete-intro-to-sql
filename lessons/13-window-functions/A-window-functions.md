> Back to movies. `\c omdb` to reconnect to the movies db.

Let's chat a moment about window functions. We saw one briefly in our Node.js project where we needed it for the count of the whole table.

What if we wanted aggregate information about part of our rows. Basically a GROUP BY for an individual row.

Here's an example: our movies table has vote_averages in it. What if for each row we also wanted to see the average vote_average for the whole table?

```sql
SELECT
  name, kind, vote_average, AVG(vote_average) OVER () AS all_average
FROM
  movies
LIMIT 50;
```

This adds an `all_average` column that will be the average vote_average for the entire table. The OVER says we're going to use a window function and the `()` means we're not going to do any more slicing and dicing: we just want to include the whole result set.

This is sorta useful but what if we wanted to be a bit more granular than that? What we if wanted to see the average of its `kind` (the kind in this table is movie, movieseries, episode, series, etc.)? We can do that with a PARTITION BY statement.

```sql
SELECT
  name, kind, vote_average, AVG(vote_average) OVER (PARTITION BY kind) AS kind_average
FROM
  movies
LIMIT 50;
```

> You may need to add a `WHERE kind='episode'` to see how they can be different since this database is mostly movies.

The `PARTITION BY` statement is telling PostgreSQL how to slice and dice for the averages. In this case we're saying "give us the average by kind". So if you see a movie, its `kind_average` will be the average of all movies. If you see an episode, its `kind_average` will the average of all episodes.

To see this together with distinct, let's look at:

```sql
SELECT DISTINCT
  kind, AVG(vote_average) OVER (PARTITION BY kind) AS kind_vote_average
FROM movies;
```

This now allows us to see at a glance the averages of each kind in a nice table.
