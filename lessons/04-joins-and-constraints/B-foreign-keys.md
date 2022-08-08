---
description: ""
---

So far so good, we have the ability to query across tables and join them together. We have a problem though: what if we delete a recipe?

```sql
DELETE
FROM
  recipes r
WHERE
  r.recipe_id = 5;
-- The khachapuri
```

Cool, we dropped the recipe, but what about the photos?

```sql
SELECT
  *
FROM
  recipes_photos rp
WHERE
  rp.recipe_id = 5;
```

Oh no! Still there! Now, we could write a second query to drop these two, but it'd be great if Postgres could track these changes for us and assure us they'd never fall out of sync.

Let's start with a fresh slate really quick.

```sql
DROP TABLE IF EXISTS recipes;
DROP TABLE IF EXISTS recipes_photos;
CREATE TABLE recipes (
  recipe_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  title VARCHAR ( 255 ) UNIQUE NOT NULL,
  body TEXT
);
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

Now, let's see what happens if we modify recipes_photos really quick.

```sql
CREATE TABLE recipes_photos (
  photo_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  url VARCHAR(255) NOT NULL,
  recipe_id INTEGER REFERENCES recipes(recipe_id) ON DELETE CASCADE
);
```

Okay, so we have a few things here

- The REFERENCES portion means it's going to be a foreign key. You tell it what it's going to match up to. In our case `recipes` is the table and `recipe_id` is the name of the column it'll match. In our case those are the same name, but it doesn't have to be. It must be the primary key of the other table.
- Then you need to tell it what to do when you delete something. With `ON DELETE CASCADE` you say "if the row in the other table gets deleted, delete this one too." So if we delete something from recipes, it will automatically delete all its photos. Pretty cool, right?
- You can also do `ON DELETE SET NULL` which does exactly what it says it does. There's also `ON DELETE NO ACTION` which will error out if you try to delete something from recipes if there are still photos left. This forces developers to clean up photos before deleting recipes. That can be helpful to.
- There's also `ON UPDATE`s if you need to handle some synced state state between the two tables.

If you're going to have have two tables reference each other, use foreign keys where possible. It makes useful constraints to make sure delete and update behaviors are intentional and it makes the queries faster.
