Notice in our query above that "xiao long bao" does not show up at all. Makes sense, we have no photos of xiao long bao so why would they show up? INNER JOIN was the correct choice for what semantics we intended.

Okay, but let's say we were populating a list of all our recipes and their photos and we had a default image if a recipe didn't have any photos? Then INNER JOIN doesn't make sense because INNER JOIN only gives up things where they exist in both table A and table B. So how would we accomplish this task then?

```sql
SELECT r.title, r.body, rp.url
  FROM recipes_photos rp
  RIGHT OUTER JOIN
    recipes r
  ON
    rp.recipe_id = r.recipe_id;
```

Or just

```sql
SELECT r.title, r.body, rp.url
  FROM recipes_photos rp
  RIGHT JOIN
    recipes r
  ON
    rp.recipe_id = r.recipe_id;
```

(The OUTER is optional.)

So what is this? This is saying "join everything has a match (aka everything in INNER JOIN) as a row. Leave out everything in the table in the `FROM` clause that doesn't have a match (which in this case would mean leave out any photos that aren't matched to a recipe. We don't have any anyway and shouldn't. Orphan photos would just be bloat.)

The `RIGHT` part of this means "include all recipes without photos". That's what the RIGHT part means. If you wanted any photos included that didn't have recipes, you guessed it, you'd use `LEFT`.

What if we want both?

```sql
SELECT r.title, r.body, rp.url
  FROM recipes_photos rp
  FULL OUTER JOIN
    recipes r
  ON
    rp.recipe_id = r.recipe_id;
```

This is a combination of LEFT and RIGHT join, pluse the INNER.

[![diagram of SQL joins](../images/SQL_Joins.png)](https://commons.wikimedia.org/wiki/File:SQL_Joins.svg)

As you can see here in this diagram, you can select for any overlap of the two Venn diagrams. They're all "correct"; it just depends what you mean to select. There's no one correct way. That's like asking "is + or - correct?" Well, it depends on what you're trying to do!

## NATURAL JOIN

I intentionally named `recipe_id` in both tables the same to show you this fun party trick.

```sql
SELECT *
  FROM recipes_photos
  NATURAL JOIN
    recipes;
```

What is this sorcery!? Because we named recipe_id the same in both, Postgres is smart enough to put two and two together and figure out that that's what we should join on.

`NATURAL JOIN` is short for `NATURAL INNER JOIN`. `NATURAL LEFT JOIN` and `NATURAL RIGHT JOIN` are also possible too.

In general let me steer you away from this in your code. Your tables can change down the line and you could accidentally name things the same that aren't the same (like `id` being a classic one.) It's useful for quick querying like we're doing, but I'd say be explicit and avoid NATURAL's implicit behavior. Still cool though, right?

## CROSS JOIN

A small note on a not super useful type of JOIN. Let's say you had two tables. In table 1 you had colors: green, blue, and red. In table 2 you have animals: dog, cat, and chicken. You want to make every possible permutation of the combination of both tables: green dog, green cat, green chicken, blue dog, blue cat, etc. There's an ability to do this with CROSS JOIN

```sql
SELECT r.title, r.body, rp.url
  FROM recipes_photos rp
  CROSS JOIN
    recipes r;
```

Keep in mind that 3 rows in each table yielded 9 rows. In our case, it yield 78 rows! It's not typically the most useful kind of join buut good to know it's there.
