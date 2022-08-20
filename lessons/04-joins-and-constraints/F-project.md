---
description: ""
---

Your next project is the recipes side of the page. Here you will have a list of recipes, and if a user clicks on one of the recipes, they will be taken to the recipe page where they will see the recipe, the description, all the photos, and all the ingredients.

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

[Here's a big SQL query which a bunch of recipes.][recipe] This will tear down all your tables and recreate them! This is helpful if you need to restart but be aware if you've added things this will delete them.

Again, you will only be editing recipes/api.js. You _can_ edit other things if you want to but it shouldn't be necessary.

Alright, good luck! Tweet me your results!

[recipe]: /recipes.sql
