---
description: ""
---

What should you do if you find you referring to the same query over and over again? We have several shortcuts to store frequently used functions in PostgreSQL. In the next chapter we'll talk about views which is one way to do it but we are going to talk about functions and procedures in this chapter. Let's learn about functions in this section and in the next section when you learn about procedures we will talk about the difference is between the two.

A function is just like you expect from a programming lanugage. Given parameters, it will return an output of some variety.

Let's say we're focusing our recipe site on recipes with few ingredients (for lazy people, like me.) Let's say we give users a way to filter recipes by how many ingredients are in the recipe.

```sql
SELECT
  r.title
FROM
  recipe_ingredients ri

INNER JOIN
  recipes r
ON
  r.recipe_id = ri.recipe_id

GROUP BY
  r.title
HAVING
  COUNT(r.title) BETWEEN 4 AND 6;
```

This will give you all recipes that are have 4, 5, or 6 ingredients in them. Okay, let's saying this is a big focus of our website and we're going to repeat this everywhere, it would be annoying to copy and paste this everywhere. Wouldn't be cool if PostgreSQL would let us wrap this up into an easy to use function?

Ask and you shall receive.

```sql
CREATE OR REPLACE FUNCTION
  get_recipes_with_ingredients(low INT, high INT)
RETURNS
  SETOF VARCHAR
LANGUAGE
  plpgsql
AS
$$
BEGIN
  RETURN QUERY SELECT
    r.title
  FROM
    recipe_ingredients ri

  INNER JOIN
    recipes r
  ON
    r.recipe_id = ri.recipe_id

  GROUP BY
    r.title
  HAVING
    COUNT(r.title) between low and high;

END;
$$;
```

> You could accomplish this several ways. Functions is just an easy to do this.

Now query it.

```sql
SELECT * FROM get_recipes_with_ingredients(2, 3);
```

Now we have a function that we can call from whenever we need to our more complicated query. This can come in very handy if your team has shared needs.

Let's break down some features of it:

- You can just say CREATE or you can just say REPLACE. I added both so it wouldn't trip you up
- `DROP FUNCTION get_recipes_with_ingredients(low int, high int)` will delete the function
- You do need to declare a return type, either in the function definiton like we did, or in the invocation
- `$$` is called "dollar quotes". You can actually use them instead of `'` in your insertions if you're sick of writing `''` for your single quotes and `\\` for your backslashes. It's just really ugly. Here we use them because we have a long quote and it would be very annoying to try and escape everything
- This language is called PL/pgSQL. It's a very SQL like language designed to be easy to write functions and procedures. PostgreSQL actually allows itself to be extended and you can use [JavaScript][js], [Python][py], and other languages to write these as well
- Here we're just returning the result of a query. There's a myriad of more powerful things you can do with functions and fun sorts of aggregation. I'll leave that to you to explore.

A note from your potential future self or coworker: _do not go overboard_. This are powerful but they can also be weird to debug. Where do you store the code for these? How are you going to debug it when it has a bug in production? There are ways to handle these things but it's _different_ than just handling it in your server's code. Use with caution.

[js]: https://plv8.github.io/
[py]: https://www.postgresql.org/docs/current/plpython.html
