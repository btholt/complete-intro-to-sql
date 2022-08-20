Let's say you have a view where you want to see the biggest different between revenue and budget (more-or-less profit but keep in mind it's all [Hollywood accounting][hollywood] anyway).

This is a derived value. It's the budget column subtracted from the revenue column. If you run this query as is it's really expensive.

```sql
EXPLAIN ANALYZE SELECT
  name, date, revenue, budget, COALESCE((revenue - budget), 0) AS profit
FROM
  movies
ORDER BY
  profit DESC
LIMIT 10;
```

What if this is a hot path for us? We don't want such a slow query for an important part of our app. We can make a index on a derived value and PostgreSQL will be smart enough to use it.

```sql
CREATE INDEX idx_movies_profit ON movies (COALESCE((revenue - budget), 0));

EXPLAIN ANALYZE SELECT
  name, date, revenue, budget, COALESCE((revenue - budget), 0) AS profit
FROM
  movies
ORDER BY
  profit DESC
LIMIT 10;
```

The difference here is stark. For my computer it's `cost=7258.76..7259.91 ` for the slow query and `cost=0.42..1.33` for the indexed query.

## This is just a taste

I've shown you just a taste of indexing. Optimizng queries is a whole profession unto itself and anything much more complicated than this I would probably consult a [DBA][dba] or another expert to help me craft well.

The key here is you typically want to use some sort of monitoring software to watch for slow queries that you're running frequently and then optimize for those. If you have a slow query that only gets run once a day then that's fine for that to be slow (usually, you also don't want to lock your whole database once a day either) or if you have a query that gets run a lot but PostgreSQL is already running it fast then you probably don't need an index either. You want indexes for frequently run queries that are running slow.

[hollywood]: https://en.wikipedia.org/wiki/Hollywood_accounting
[dba]: https://en.wikipedia.org/wiki/Database_administration
