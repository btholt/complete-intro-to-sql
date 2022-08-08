---
description: ""
---

So far we've done a one to one matching of records. We've used a record in a database to represent one item: a vegetable, a fruit, so on and so forth.

Now we're going to get into records that can relate to each other. Let's think about recipes. A recipe has multiple ingredients. That **has** word is key here. It means there is a relationship. A single recipe has many ingredients. An ingredient can also be in many recipes. A tomato is both in pizza sauce and in a BLT. This is called a many-to-many relationship.

There's also one-to-many relationships, like imagine if we had multiple photos of each of our ingredients. A single ingredient will have five photos. And those photos will only will only belong to one ingredient. A photo of a green pepper doesn't make sense to belong to anything besides the green pepper ingredient.

There can also exist one-to-one relationships but in general you would just make those the same record all together. You could split up the type and title into two tables, but why would you.

## A basic relationship

We're going to do this a naïve wrong way, and then we're going to do it the correct way.

First thing we're going to make a recipes table

```sql
CREATE TABLE recipes (
  recipe_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  title VARCHAR ( 255 ) UNIQUE NOT NULL,
  body TEXT
);
```

Okay, no rocket science so far. We just created another random table. Let's say we could have many images of a single recipe. How would we go about doing that?

```sql
INSERT INTO recipes
  (title, body)
VALUES
  ('cookies', 'very yummy'),
  ('empanada','ugh so good'),
  ('jollof rice', 'spectacular'),
  ('shakshuka','absolutely wonderful'),
  ('khachapuri', 'breakfast perfection'),
  ('xiao long bao', 'god I want some dumplings right now');
```

> Pro tip: don't write a course while hungry.

Okay, we now have a bunch of recipes. Our problem now is that we have a variable amount of photos for each recipe. Perhaps we have one picture of cookies but we have four pictures of jollof rice. How do we model that if we always have a fixed amount columns?

One approach would be to say "I only have at most five pictures of any recipes, I'll make columns pic1 through pic5 columns and limit myself to that". Totally valid approach, and if you can guarantee only 5 images at most, it might be the right approach.

But what if we can't guarantee that? We need some sort of flexibility. This is where we can use two tables.

```sql
CREATE TABLE recipes_photos (
  photo_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  recipe_id INTEGER,
  url VARCHAR(255) NOT NULL
);
```

Now we can insert a some rows to reference images that we have.

```sql
INSERT INTO recipes_photos
  (recipe_id, url)
VALUES
  (1, 'cookies1.jpg'),
  (1, 'cookies2.jpg'),
  (1, 'cookies3.jpg'),
  (1, 'cookies4.jpg'),
  (1, 'cookies5.jpg'),
  (2, 'empanada1.jpg'),
  (2, 'empanada2.jpg'),
  (3, 'jollof1.jpg'),
  (4, 'shakshuka1.jpg'),
  (4, 'shakshuka2.jpg'),
  (4, 'shakshuka3.jpg'),
  (5, 'khachapuri1.jpg'),
  (5, 'khachapuri2.jpg');
-- no pictures of xiao long bao
```

Okay, now let's select all the photos for shakshuka. Shakshuka is ID 4 (if you followed my instructions, feel free to drop tables and recreate them if you need to.)

```sql
SELECT title, body FROM recipes WHERE id = 4;
SELECT url FROM recipes_photos WHERE recipe_id = 4;
```

It'd be cool if we could do these all at the same time. Some sort inner section of a venn diagram, right? You can!

```sql
SELECT recipes.title, recipes.body, recipes_photos.url
  FROM recipes_photos
  INNER JOIN
    recipes
  ON
    recipes_photos.recipe_id = recipes.recipe_id
  WHERE recipes_photos.recipe_id = 4;
```

Amazing!!

Now we're getting data from each table smushed together. The data from recipes will be repeated over item in the recipes_photos table but that's what we expect.

Just to show you some common shorthand (but otherwise same query)

```sql
SELECT r.title, r.body, rp.url
  FROM recipes_photos rp
  INNER JOIN
    recipes r
  ON
    rp.recipe_id = r.recipe_id
  WHERE rp.recipe_id = 4;
```

Go ahead and try this to see the full inner join.

```sql
SELECT r.title, r.body, rp.url
  FROM recipes_photos rp
  INNER JOIN
    recipes r
  ON
    rp.recipe_id = r.recipe_id;
```

Pretty cool, right?

## Other types of joins

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
