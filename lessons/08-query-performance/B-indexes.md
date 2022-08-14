## Identifying what to index

We've identified some slow queries and how to diagnose them as slow. We've identified what a index scan is versus what a sequential scan is. Here we're going to learn how to index things and when we want to do that.

First thing is, _don't index everything_. Indexes take space and do incur some overhead for PostgreSQL to decide if it's going to use (using what's called the query planner.) If you have data that's rarely accessed or frequently filtered out then don't index it!

So what do you index? Things you query frequently that have to filter out lots of rows.

## It's okay that sometimes that the planner doesn't use the index

Another thing to keep in mind is indexes are only useful when you're filtering data out or doing some sort of different sort. It all depends on what you want to do.

Think of it like a dictionary and the page numbers are like indexes you can query on. If you're looking for one word, 'defenestrate', it's easy to use the page numbers / index to look up that one query quickly. You don't have to scan every row / word on the As-Cs to find the word: you can skip until you're close.

However what if your goal is to read the dictionary cover to cover? What use are indexes are you for then? You wouldn't use the page numbers at all in this case (and neither would the planner.) Therefore the planner would still use sequential scan because adding an index just adds unnecessary overhead.

This isn't just for complete datasets either. Imagine you're reading the whole dataset _except_ the Xs. The X section is so short you'd probably just scan over them to get to the Ys. Same thing with the planner: it'll make a call sometimes to just scan over rows because adding an index would be too much overhead. Basically what I'm saying is that the planner is usually right even if it's unintuitive.

## Unique indexes

So we've seen and used one sort of index already: primary keys (which are normally sequentially increasing integers called `id` or similar.)

Unique and primary keys create indexes by default and you don't need to do anything else to get them. They do this because everytime you insert into a unique key it needs to go check if it already exists so it can enforce uniqueness, making it necessary to have an index.

Keep in mind you can also have uniqueness as a constraint across multiple columns. Imagine you stored address, city, and state as three columns. Multiple people could live on `1000 4th Street` across Seattle, San Francisco, Minneapolis, and Savannah, but there can only be one `1000 4th Ave, Seattle, WA`. Thereforce a unique constraint across multiple columns could be really useful. You'd do that with something like

```sql
CREATE TABLE american_addresses (
    id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    street_address int NOT NULL,
    city VARCHAR(255) NOT NULL,
    state VARCHAR(255) NOT NULL,
    UNIQUE (street_address, city, state)
);
```

## Let's create an index

Okay, so we've identified that we frequently query movies by exact names and this is a query we want to support. It's a big table (175721 rows) and therefore a sequential scan on this is _pricy_. So how we go about doing that?

```sql
EXPLAIN ANALYZE SELECT * FROM movies WHERE name='Tron Legacy';

CREATE INDEX idx_name ON movies(name);

EXPLAIN ANALYZE SELECT * FROM movies WHERE name='Tron Legacy';
```

Check out the difference there. On my machine I'm seeing a difference between a cost of `4929.51` and `12.30` for the total cost. _Wild_, right?

Do note I'm also seeing a difference in the startup cost. A sequential scan has a startup cost `0.00` or at least very, very low and the cost of the index for me is `4.44`. This is why sometimes using an index isn't worth it for the planner: using an index isn't _free_. You incur overhead to use it. Think of it doing something like memoization in your code. You don't memoize everything because frequently it buys you nothing with lots of additional code and overhead. Similar idea here.

## B-tree versus others

When you say `CREATE INDEX` you are implicitly saying you want a b-tree index. A [b-tree][btree] is a data structure that is specifically easy to search.

I teach a lesson on [Frontend Masters][fem] on how to do AVL trees which are a simplification of b-trees if you want to get into how a self-balancing tree works.

B-trees are compact, fairly simple data structures that are suited to general indexes. However there are a few more to be aware of. We're about to talk about GIN indexes next so let's cover the ones first we're not going to be talking about.

- Hash - Useful for when you're doing strict equality checks and need it fast and in memory. Can't handle anything other than `=` checks.
- GiST - Can also be used for full text search but ends up with false positives sometimes so GIN is frequently preferred.
- SP-GiST - Another form of GiST. Both GiST and SP-GiST offer a variety of different searchable structures and are much more special-use-case. I've never had to use either. SP-GiST is most useful for clustering types of search and "nearest neighbor" sorts of things.
- BRIN - BRIN is most useful for extremely large tables where maintaing a b-tree isn't feasible. BRIN is smaller and more compact and therefore makes larger table indexing more feasible.

[This page from the docs about index types][indexes] is helpful to glance at.

## GIN and JSONB

GIN stands for generalized inverted index. It's another data structure for indexes. In this case it's really useful for things where multiple values could apply to one row. A good example of this would be with JSONB columns: you have one column but it could have multiple values inside the object that a search could find. Fathom the below example:

> If you run these queries, you'll see it's still using sequential scan. You need pretty big tables (200+ rows? that's an estimate) for it to start being worth it to use your index.

```sql
CREATE TABLE movie_reviews (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  movie_id INTEGER,
  scores JSONB NOT NULL
);

INSERT INTO
  movie_reviews
  (movie_id, scores)
VALUES
  (21103, '{ "rotten_tomatoes": 94, "washington_post": 50, "nytimes": 45 }'),
  (97, '{ "rotten_tomatoes": 87, "washington_post": 40 }'),
  (18235, '{ "rolling_stone": 85, "washington_post": 60, "nytimes": 35 }'),
  (10625, '{ "rotten_tomatoes": 100, "washington_post": 100, "nytimes": 100, "rolling_stone": 100 }'),
  (85014, '{ "nytimes": 67 }'),
  (2493, '{ "rotten_tomatoes": 67, "rolling_stone": 89, "nytimes": 85 }'),
  (11362, '{ "rotten_tomatoes": 76, "washington_post": 14, "nytimes": 98 }'),
  (674, '{ "rotten_tomatoes": 78, "washington_post": 40, "nytimes": 77, "rolling_stone": 54 }');

CREATE INDEX ON movie_reviews USING gin(scores);
EXPLAIN ANALYZE SELECT * FROM movie_reviews WHERE scores ? 'rolling_stone';
EXPLAIN ANALYZE SELECT *  FROM movie_reviews WHERE scores @> '{"nytimes": 98}';
```

The above queries on a large table would be a great fit for a GIN index.

## GIN and Full Text Search

GIN, like we said before, is good for things where you can have one column that have multiple values that can return true. So what if we took our search term (in this case let's search for `star wars`) and broke it down in smaller, searchable pieces? Like, three letter pieces, or as they're called, _trigrams_. This is one way PostgreSQL can handle full text search.

```sql
SELECT SHOW_TRGM('star wars');
```

This shows you how PostgreSQL will break down star wars into multiple trigrams. It'll then search based on these and will use GIN to index these in the same fashioned it can index JSONB. Wild one technology works so well in two different ways.

```sql
EXPLAIN ANALYZE SELECT * FROM movies WHERE name ILIKE '%star wars%';

CREATE INDEX ON movies USING gin(name gin_trgm_ops);

EXPLAIN ANALYZE SELECT * FROM movies WHERE name ILIKE '%star wars%';
```

The `gin_trgm_ops` specifies the kind of indexing we want to do here and we're saying we want `trgm` or trigram operations. This is what you do for full text search.

You should see a large difference between the two queries. My machine got `cost=0.00..4929.51` for the first query and `cost=28.13..88.65` for the second query.

> As you can see, using the index is nearly half the cost of the second query and which is why we'd need a pretty big data set for the JSONB query to use the index.

## Partial indexes

You can also partially index tables. Let's take a look at category_names;

```sql
\d category_names

SELECT * FROM category_names WHERE language = 'en' LIMIT 5;
SELECT COUNT(*) FROM category_names;
SELECT DISTINCT language, COUNT(*) FROM category_names GROUP BY language;
```

We have a big table with 30,000+ rows but it has multiple different languages in the table. Let's say we're _mostly_ querying just the English tags and don't need to refer to the other languages as much. Instead of indexing _everything_ wastefully, we can do a partial index.

```sql
CREATE INDEX idx_en_category_names ON category_names(language) WHERE language = 'en';
EXPLAIN ANALYZE SELECT * FROM category_names WHERE language='en' AND name ILIKE '%animation%' LIMIT 5;
EXPLAIN ANALYZE SELECT * FROM category_names WHERE language='de' AND name ILIKE '%animation%' LIMIT 5;
```

Not as much of a difference here as the table isn't _that_ big and the overhead of the index is not helpful as it could be. Still, on my computer I see the `en` query's cost as `cost=133.69..643.89` and the `de` query's as `cost=0.00..833.55`. Still better!

## Derivative value indexes

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

[btree]: https://wikipedia.org/wiki/B-tree
[fem]: https://frontendmasters.com/courses/computer-science-v2/self-balancing-avl-tree/
[geo]: https://www.postgresql.org/docs/current/datatype-geometric.html
[indexes]: https://www.postgresql.org/docs/current/indexes-types.html
[hollywood]: https://en.wikipedia.org/wiki/Hollywood_accounting
[dba]: https://en.wikipedia.org/wiki/Database_administration
