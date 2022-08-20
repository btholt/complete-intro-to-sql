---
title: "GIN"
---

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
