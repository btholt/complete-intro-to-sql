---
description: ""
title: "Many-to-Many Relationships"
---

So let's now make our recipes connect to our ingredients!

This is a bit different because it's a many-to-many relationship. A recipe has many ingredients, and an ingredient can belong to many recipes (eggs are in both khachapuri and cookies.)

```sql
CREATE TABLE recipe_ingredients (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  recipe_id INTEGER REFERENCES recipes(recipe_id) ON DELETE NO ACTION,
  ingredient_id INTEGER REFERENCES ingredients(id) ON DELETE NO ACTION
);
```

We set this to error because we should clear out connections before we let developers delete recipes or ingredients. We don't want to cascade deletes because that could delete recipes and ingredients unintentionally and we don't want to set to null because then we'd have a bunch of half-null connections left over.

This table will describe the many-to-many relationship with two foreign keys between ingredients and recipes. Now we can insert records into here that describe how an ingredient belongs to a recipe.

```sql
INSERT INTO recipe_ingredients
  (recipe_id, ingredient_id)
VALUES
  (1, 10),
  (1, 11),
  (1, 13),
  (2, 5),
  (2, 13);
```

> These recipes are nonsensical, don't expect them to be correct recipes.

This is the way to connect multiple columns together. You have two tables that represent the two distinct concepts (ingredients and recipes) and then you use another table to describe the relationships between them.

So now we have two recipes that have ingredients, cookies and empanadas. Since we inserted the cookies first, it will very likely have the id of 1 (unless you have inserted some other things.) Let's select those records.

```sql
SELECT
  ri.id AS connection_id,
  i.title AS ingredient_title,
  i.image AS ingredient_image,
  i.type AS ingredient_type
FROM
  recipe_ingredients ri
INNER JOIN
  ingredients i
ON
  i.id = ri.ingredient_id
WHERE
  ri.recipe_id = 1;
```

Inner join is what we're looking for because we are only looking for rows that have connections on both sides.
