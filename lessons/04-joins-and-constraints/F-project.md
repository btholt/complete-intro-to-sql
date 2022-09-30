---
description: ""
---

Your next project is the recipes side of the page. Here you will have a list of recipes, and if a user clicks on one of the recipes, they will be taken to the recipe page where they will see the recipe, the description, all the photos, and all the ingredients.

You'll need to repopulate your `recipes_photos` table before beginning the project

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

Alternatively, here's a [big SQL query which a bunch of recipes.][recipe] This will tear down all your tables and recreate them! This is helpful if you need to restart but be aware if you've added things this will delete them.

Here's a hint on selecting just the first image for all the recipe photos.

```sql
SELECT DISTINCT ON (r.recipe_id)
  *
FROM
  recipes r
LEFT JOIN
  recipes_photos rp
ON
  r.recipe_id = rp.recipe_id;
```

That distinct ensures that we only have one recipe per image and not a bunch of rows of images and rows. The distinct bit means that's only one `recipe.recipe_id` is allowed in this results set. If we left part out, we'd get all the photos back which we don't need for the first page.

For the second page, you will likely need to query the database twice. That's just fine. Only have one query where it makes sense.

Again, you will only be editing recipes/api.js. You _can_ edit other things if you want to but it shouldn't be necessary.

Alright, good luck! Tweet me your results!

[recipe]: /recipes.sql
