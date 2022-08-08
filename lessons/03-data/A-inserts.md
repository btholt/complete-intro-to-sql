---
description: ""
---

From the previous lesson we have an ingredients table in our foodly database. It's basically an empty spreadsheet at this point. It's a repository of data with nothing in it. Let's add our first bit of data into it.

```sql
INSERT INTO ingredients (
 title, image, type
) VALUES (
  'red pepper', 'red_pepper.jpg', 'vegetable'
);
```

This is the standard way of doing an insert. In the first set of parens you list out the column names then in the values column you list the actual values you want to insert.

Big key here which will throw JS developers for a loop: you _must_ use single quotes. Single quotes in SQL means "this is a literal value". Double quotes in SQL mean "this is an identifier of some variety".

```sql
INSERT INTO "ingredients" (
 "title", "image", "type"
) VALUES (
  'broccoli', 'broccoli.jpg', 'vegetable'
);
```

The above query works because the double quotes are around identifiers like the table name and the column names. The single quotes are around the literal values. The double quotes above are optional. The single quotes are not.

Okay, let's insert a few more.

```sql
INSERT INTO ingredients (
  title, image, type
) VALUES
  ( 'avocado', 'avocado.jpg', 'fruit' ),
  ( 'banana', 'banana.jpg', 'fruit' ),
  ( 'beef', 'beef.jpg', 'meat' ),
  ( 'black_pepper', 'black_pepper.jpg', 'other' ),
  ( 'blueberry', 'blueberry.jpg', 'fruit' ),
  ( 'broccoli', 'broccoli.jpg', 'vegetable' ),
  ( 'carrot', 'carrot.jpg', 'vegetable' ),
  ( 'cauliflower', 'cauliflower.jpg', 'vegetable' ),
  ( 'cherry', 'cherry.jpg', 'fruit' ),
  ( 'chicken', 'chicken.jpg', 'meat' ),
  ( 'corn', 'corn.jpg', 'vegetable' ),
  ( 'cucumber', 'cucumber.jpg', 'vegetable' ),
  ( 'eggplant', 'eggplant.jpg', 'vegetable' ),
  ( 'fish', 'fish.jpg', 'meat' ),
  ( 'flour', 'flour.jpg', 'other' ),
  ( 'ginger', 'ginger.jpg', 'other' ),
  ( 'green_bean', 'green_bean.jpg', 'vegetable' ),
  ( 'onion', 'onion.jpg', 'vegetable' ),
  ( 'orange', 'orange.jpg', 'fruit' ),
  ( 'pineapple', 'pineapple.jpg', 'fruit' ),
  ( 'potato', 'potato.jpg', 'vegetable' ),
  ( 'pumpkin', 'pumpkin.jpg', 'vegetable' ),
  ( 'raspberry', 'raspberry.jpg', 'fruit' ),
  ( 'red_pepper', 'red_pepper.jpg', 'vegetable' ),
  ( 'salt', 'salt.jpg', 'other' ),
  ( 'spinach', 'spinach.jpg', 'vegetable' ),
  ( 'strawberry', 'strawberry.jpg', 'fruit' ),
  ( 'sugar', 'sugar.jpg', 'other' ),
  ( 'tomato', 'tomato.jpg', 'vegetable' ),
  ( 'watermelon', 'watermelon.jpg', 'fruit' )
ON CONFLICT DO NOTHING;
```

> Feel free to copy and paste this. Too much typing.

This is the way to do many inserts at once, just by comma separating sets in the VALUES part.

Note the `ON CONFLICT` section. Some of these you may have already inserted (like the red pepper.) This is telling PostgreSQL that if a row exists already to just do nothing about it. We could also do something like:

```sql
INSERT INTO ingredients (
  title, image, type
) VALUES
  ( 'watermelon', 'banana.jpg', 'this won''t be updated' )
ON CONFLICT (title) DO UPDATE SET image = excluded.image;
```

This is what many of us would call an "upsert". Insert if that title doesn't exist, update if it doesn't. If you try to run that query (or the previous one) without the ON CONFLICT statement, it will fail since we asserted that title is a UNIQUE field; there can only be one of tha exact field in the database.

The type won't be updated because we didn't choose to handle that.

Also notice we did two `'` in a row (and not a double quote, but two single quotes in a row.) Because " and ' have different meanings in SQL, we have to account for that. In this case we do that if we want to have a single quote in our string.
