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
