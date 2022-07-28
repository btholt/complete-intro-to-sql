---
description: ""
---

The next thing we need to learn is how to update a field. We've seen how to INSERT and do an update if an INSERT fails, but let's see how to update something directly.

```sql
UPDATE ingredients SET image = 'watermelon.jpg' WHERE title = 'watermelon';
```

The WHERE clause is where you filter down what you want to update. In this case there's only one watermelon so it'll just update one but if you had many watermelons it would match all of those and update all of them,

You probably got a line returned something like

```plaintext
UPDATE 1
```

If you want to return what was updated try:

```sql
UPDATE ingredients SET image = 'watermelon.jpg' WHERE title = 'watermelon' RETURNING id, title, image;
```

The RETURNING clause tells Postgres you want to return those columns of the things you've updated. In our case I had it return literally everything we have in the table so you could write that as

```sql
UPDATE ingredients SET image = 'watermelon.jpg' WHERE title = 'watermelon' RETURNING *;
```

The \* means "everything" in this case.

Let's add two rows with identical images.

```sql
INSERT INTO ingredients
  (title, image)
VALUES
  ('not real 1', 'delete.jpg'),
  ('not real 2', 'delete.jpg');
```

> Whitespace isn't significant in SQL. You can break lines up however you want and how they make sense to you.

Now let's update both.

```sql
UPDATE ingredients
SET image = 'different.jpg'
WHERE image='delete.jpg'
RETURNING id, title, image;
```

Here you can see as long as that WHERE clause matches multiple rows, it'll update all the rows involved.

## DELETE

Deletes are very similar in SQL.

```sql
DELETE FROM ingredients
WHERE image='different.jpg'
RETURNING *;
```

Here we just have no SET clause. Anything that matches that WHERE clause will be deleted. The RETURNING, like in updates, is optional if you want to see what was deleted.
