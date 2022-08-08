---
description: ""
---

Let's add a JSONB field to our recipes. We'll call it `meta` as in metadata but we could call it anything. And keep in mind this is just a normal JSON object. You can have arrays, nested objects, whatever.

```sql
ALTER TABLE recipes
ADD COLUMN meta JSONB;
```

That's easy enough. Okay, let's now add a few rows to it. Keep in mind that it does have to be valid JSON or Postgres won't let you add it.

```sql
UPDATE
  recipes
SET
  meta='{ "tags": ["chocolate", "dessert", "cake"] }'
WHERE
  recipe_id=16;

UPDATE
  recipes
SET
  meta='{ "tags": ["dessert", "cake"] }'
WHERE
  recipe_id=20;

UPDATE
  recipes
SET
  meta='{ "tags": ["dessert", "fruit"] }'
WHERE
  recipe_id=45;

UPDATE
  recipes
SET
  meta='{ "tags": ["dessert", "fruit"] }'
WHERE
  recipe_id=47;
```

Okay, let's select all tags from any recipe that has meta data.

```sql
SELECT meta -> 'tags' FROM recipes WHERE meta IS NOT NULL;
```

The `->` is an accessor. In JS terms, this is like saying `meta.tags`. If we were accessing another layer of nesting, you just use more `->`. Ex. `SELECT meta -> 'name' -> 'first'`.

Let's try selecting just the first element of each one using that.

```sql
SELECT meta -> 'tags' -> 0 FROM recipes WHERE meta IS NOT NULL;
```

This should give you back only the first item of each. Notice they're still weirdly in `""`. This is because the `->` selector gives you back a JSON object so you can keep accessing it. If you want it back as just text, you use `->>`.

```sql
SELECT meta -> 'tags' ->> 0 FROM recipes WHERE meta IS NOT NULL;
```

Notice the first one `->` because we need tags back as a JSON object (because accessors don't work on text) but we use `->>` on the second one so we can get the text back. Not always important but it bit me a few times and I wanted to help you.

Alright, let's select all the cakes.

```sql
SELECT recipe_id, title, meta -> 'tags' FROM recipes WHERE meta -> 'tags' ? 'cake';
SELECT recipe_id, title, meta -> 'tags' FROM recipes WHERE meta -> 'tags' @> '"cake"';
```

Both of these work. The first one with the `?` checks to see if 'cake' is a top level key available in which case it is.

The second with the `@>` is doing a "does this array contains this value. That's why we do need the extra double quotes, since it is a string in a JSON value.
