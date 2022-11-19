---
description:
---

Some times you want to search for something in your database but it's not an exact match. The best example would be if a user types something into the search bar, they're likely not giving you something that you match _exactly_. You would expect a search for "pota" to match "potato", right? Enter `LIKE` in SQL.

```sql
SELECT * FROM ingredients WHERE title LIKE '%pota%';
```

This is a very limited fuzzy matching of text. This is not doing things like dropping "stop words" (like and, the, with, etc.) or handling plurals, or handling similar spellings (like color vs colour). Postgres _can_ do this, and we'll get there later with indexes.

## Built in functions

Okay, great, now what if a user searchs for "fruit"? We'd expect that to work, right?

```sql
SELECT * FROM ingredients WHERE CONCAT(title, type) LIKE '%fruit%';
```

`concat()` is a function that will take two strings and combine them together. We can concat our two title and type columns and then used LIKE on the results on that combined string.

> The result of the cherry row would be `cherryfruit` which means it'd match weird strings like `rryfru`. We'll talk later about more complicated string matching but this will do for now.

Okay, but what if we have capitalization problem? We can use lower, both on the columns and on the values.

```sql
SELECT * FROM ingredients WHERE LOWER(CONCAT(title, type)) LIKE LOWER('%FrUiT%');
```

`LOWER()` take a string and make it lowercase.

Fortunately, there's an even easier way to do this with less function evocation.

```sql
SELECT * FROM ingredients WHERE CONCAT(title, type) ILIKE '%FrUiT%';
```

`ILIKE` does the same thing, just with case insensitivity. Mostly I just wanted to show you lower!

There are _so many_ built in functions to Postgres. [Click here to see the official docs.][pg]

## % vs \_

You see that we've been surrounding the `LIKE` values with `%`. This is basically saying "match 0 to infinite characters". So with "%berry" you would match "strawberry" and "blueberry" but not "berry ice cream". Because the "%" was only at the beginning it wouldn't match anything after, you'd need "%berry%" to match both "strawberry" and "blueberry ice cream".

```sql
SELECT * FROM ingredients WHERE title ILIKE 'c%';
```

The above will give you all titles that start with "c".

You can put % anywhere. "b%t" will match "bt", "bot", "but", "belt", and "belligerent".

There also exists `_` which will match 1 and only one character. "b_t" will match "bot" and "but" but not "bt", "belt", or "belligerent".

[pg]: https://www.postgresql.org/docs/9.2/functions.html
