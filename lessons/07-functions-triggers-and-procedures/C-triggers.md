---
description: ""
---

Let's talk about triggers. Let's say you have a function that you want to run reactively to some event happening in your database. This is what a trigger is for.

For example, let's say we need an audit trail of the names of recipes. Maybe we have a bug we're tracking down or it's important for us to be able to say "this recipe used to be called this but we've since edited it." A trigger is perfect for this.

Quickly let's make a table that will serve as our repository for this data.

```sql
CREATE TABLE updated_recipes (
  id INT GENERATED ALWAYS AS IDENTITY,
  recipe_id INT,
  old_title VARCHAR (255),
  new_title VARCHAR (255),
  time_of_update TIMESTAMP
);
```

The `TIMESTAMP` data type is new to you but this is just a standard way of storing a time in PostgreSQL. Try `SELECT NOW()` to see what one looks like.

```sql
CREATE OR REPLACE FUNCTION log_updated_recipe_name()
  RETURNS
    TRIGGER
  LANGUAGE
    plpgsql
AS
$$
BEGIN
  IF OLD.title <> NEW.title THEN
    INSERT INTO
      updated_recipes (recipe_id, old_title, new_title, time_of_update)
    VALUES
      (NEW.recipe_id, OLD.title, NEW.title, NOW());
  END IF;
  RETURN NEW;
END;
$$;
```

Another function here but with a bit more logic. Here we're asking if the title changed between `OLD` (what was there before) and `NEW` (what is there now). If `body` updated this trigger wouldn't do anything because it only checks title. If that conditional is true we run an insert statement and then we end.

Make sure you have that RETURN NEW. Functions have to return what the SELECT would expect to get back. In this case we're just giving back NEW. You can have a trigger intercept and modify inserts, updates, etc.

Keep in mind this just creates a function but doesn't yet bind it to an event. We need ot create a trigger that runs our function.

Let's set up our trigger.

```sql
CREATE OR REPLACE TRIGGER updated_recipe_trigger
AFTER UPDATE ON recipes
  FOR EACH ROW EXECUTE PROCEDURE log_updated_recipe_name();
```

This takes our function that we created and binds it to the update events on recipes. You can do this on inserts, deletes, and all sorts of other events. We're also running this _after_ the insert happens. If you wanted to prevent certain updates or modify them, you could run this before as well.

This is a rudimentary intro to triggers but there's a lot more you can do here! This is just one thing.

> Note you can't run _procedures_ as triggers. Triggers always deal with functions. However there's nothing preventing you from `CALL`ing a procedure from a function.
