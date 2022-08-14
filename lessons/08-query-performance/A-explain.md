Let's have a chat about query performance.

How different do you think these queries are?

```sql
SELECT * FROM movies WHERE name='Tron Legacy';
SELECT * FROM movies WHERE id=21103;
```

Turns out, from a performance perspective, they are _wildly_ different. The second query is _623×_ times faster on my computer. Why? Let's find out!

```sql
EXPLAIN ANALYZE SELECT * FROM movies WHERE name='Tron Legacy';
EXPLAIN ANALYZE SELECT * FROM movies WHERE id=21103;
```

This will show you some output on the performance profile of our query.

A couple key things I want to draw to your attention:

- `cost=0.00..4988.77` vs `cost=0.42..8.44`. This cost is arbitrary unit meant to be used comparatively, like in this case.
- The first number is the startup cost. Generally for SELECTs like this it's near zero but if you're doing a sort of some variety you can incur startup cost.
- The second number is the total cost of the query. You can see here that they are _very_ different.

Here's a quote on how they measure the cost [from a great article][cost]

> The cost units are anchored (by default) to a single sequential page read costing 1.0 units (seq_page_cost). Each row processed adds 0.01 (cpu_tuple_cost), and each non-sequential page read adds 4.0 (random_page_cost). There are many more constants like this, all of which are configurable. That last one is a particularly common candidate, at least on modern hardware. We’ll look into that more in a bit.

- `Seq Scan` vs `Index Scan` is what's actually making a difference here.
- `Seq Scan` means it read _every row in the table_ to get your ansewr. Yeah, not good. You can see it read `Rows Removed by Filter: 175720` to get our one answer. Wild!
- `Index Scan` means it had access to an index to find the correct row. Remember [trees from data structures and algorithms][tree]? This is where they are used!! We can tell PostgreSQL in advance "hey, this is going to get queried in the future and I want to it to be fast, please build a tree so that you can find it faster. We'll talk about making new indexes in the next section.

## More complicated queries.

Let's grab one of our previous lesson's answers.

```sql
EXPLAIN ANALYZE SELECT
  m.name, COUNT(c.id) AS count
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

Here you'll see a variety new bits of nested information. Let's analyze (lol)

- Startup cost is almost everything here. That means it's taking a long time to get all the rows in a place to be queried. Once all the rows are in assembled and joined together the actual execution is trivial.
- We aggregated `rows=46516` in the second step into our final answer
- Our merge join was actually pretty fast because we did it on IDs that had indexes on them.
- The aggregation and the ordering took the most time here. That's the take away.

[cost]: https://scalegrid.io/blog/postgres-explain-cost/
[tree]: https://btholt.github.io/complete-intro-to-computer-science/binary-search-tree
