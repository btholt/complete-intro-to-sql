---
description: ""
---

A different-but-similar idea to functions is procedures. They're really similar and have a lot of overlap but have some slight differences.

Functions can do more. Almost everything that procedures can do functions can also do whereas vice versa is not true.

A procedure is designed to do an action of some variety. It's explictly _not_ for returning data (and cannot return data) whereas a function can return data (but does not have to.)

Let's make an example with our recipes database. Let's say we have some churn on our ingredients where images can get created and deleted and sometimes images are set to null. Let's say we wanted to _tolerate_ that (as in we don't want to make it so there's a constraint on the image column on NOT NULL) but we periodically want to go update that column to be default.jpg if there's nothing set. First, let's select everything has null for an image.

```sql
SELECT * FROM ingredients WHERE image IS NULL;
```

Nothing right now (if you've been following along with me.) So let's insert another real quick.

```sql
INSERT INTO
  ingredients
  (title, type)
VALUES
  ('venison', 'meat');
```

Okay, let's try our query again.

```sql
SELECT * FROM ingredients WHERE image IS NULL;
```

We could write an update that we run via cron job that just called `UPDATE` but let's write a procedure that does it.

```sql
CREATE PROCEDURE
  set_null_ingredient_images_to_default()
LANGUAGE
  SQL
AS
$$
  UPDATE
    ingredients
  SET
    image = 'default.jpg'
  WHERE
    image IS NULL;
$$;
```

- Notice we did not use the `plpgsql` language but the `SQL` language. Because this is a simple query without any other bells or whistles, we can use straight SQL as the language. If we needed conditionals, loops, or any other programming "stuff" we'd need something like plpgsql, JavaScript, Python, etc.
- Other than that, this looks a lot like a function with no return type. It does its action and it is done.

Okay, let's invoke this procedure now.

```sql
CALL set_null_ingredient_images_to_default();
SELECT * FROM ingredients WHERE image IS NULL;
```

You use `CALL` instead of SELECT to invoke procedures. Now we can invoke this procedure whenever we need to periodically make some maintenance to our images.

We'll expand on a great way to use procedures in the next lesson with triggers.
