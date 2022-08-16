---
description: ""
---

In our last lesson we looked at views and then the final query we ran was _super expensive_. As in `cost=293871.35..293871.40` expensive. That's so expensive that we would not want to run that on every page load.

So fathom this scenario. You show your boss this great query that shows how actors and actresses cluster around certain movie keywords and they want to ship a new page that shows users this in nice infographics. You think to yourself "oh no, that's such an expensive query, we can't do that on every page if it's really popular page."

So let's investigate the product problem

- You want to show users cool graphs with data from your database
- The query to get this data is expensive
- The data itself doesn't update too often. Only a few movies release per week and an actor/actress is only releasing a few movies a year
- This is a good place to use some sort of caching strategy

So we could defintely use some sort of cache in front of the database. We could that manages setting a memcache or Redis cache of the response and then use that and then have some cron job that updates that cache every X hours or so.

Or we could use what's called a materialized view. A materialized view is similar to a view but it's not live data. Instead you take a snapshot of what the query is now and then you query _that_ data which is way cheaper. At that point it's just a normal table. You can even index it!

Let's make a materialized view of our last query.

```sql
CREATE MATERIALIZED VIEW
  actor_categories

AS

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

GROUP BY
  arm.person_name, ecn.name

WITH NO DATA;
```

- We created the materialized view and provided it the query to get the data to populate itself.
- We specified to _not_ yet populate it. Imagine if you were doing this on your production server. You want to be _very_ judicious when you run this expensive query. If you leave out the `NO` part and say `WITH DATA` it'll run this query first time.

Now if your try `SELECT * FROM actor_categories;` you'll get a "not been populated" error. So let's populate it!

We have two options:

- `REFRESH MATERIALIZED VIEW actor_categories;` - this goes faster but it locks the table so queries in the mean time won't work.
- `REFRESH MATERIALIZED VIEW CONCURRENTLY actor_categories;` - this works far slower but doesn't lock the table in the process. Useful if you can't afford downtime on the view.

Feel free to try both but I'm going to do the first one.

Amazing, now try

```sql
SELECT * FROM actor_categories ORDER BY count DESC NULLS LAST LIMIT 10;
```

_Much faster_. `EXPLAIN ANALYZE` gives us a cost of `cost=88408.08..88409.24` which is about 1/4 of the cost.

> Note the `NULLS LAST` is necessary or else `NULLS FIRST` is implicit and it won't use our index.

The `NULLS LAST` portion isn't necessary here since nothing _should_ be null but I wanted to show you this is how you'd handle that.

So, because this is an independent source of data, it can also be indexed! We can drive this cost _way_ down on this query.

```sql
CREATE INDEX idx_actor_categories_count ON actor_categories(count DESC NULLS LAST);
```

- An index, you've seen this before.
- We added a `DESC` to the index because primarily we'll be showing the thing with the _most_ first. But keep in mind that PostgreSQL is perfectly capable of reading these indexes backwards so we can do ASC too on this same index.
- The `NULLS LAST` is wholly unnecessary but I wanted to throw it in there. Nothing in count should be null but if we did you can specify how you want it sorted.

Okay, let's query it.

```sql
EXPLAIN ANALYZE SELECT * FROM actor_categories ORDER BY count DESC NULLS LAST LIMIT 10;
```

A cost of `cost=0.43..0.75` as compared to the original cost of `cost=293871.35..293871.40`. Amazing. This is about a 40,000x increase in performance boost. Not too bad I'd say!
