> This lesson uses the recipeguru database again. Use `\c recipeguru` to get back to the recipeguru database.

I know we've been dealing with recipes which has a high tolerance for errors, particularly around making multiple inserts and updates in one query. It's not life threatening if we accidentally publish a recipe without a photo or an ingredient that isn't attached to a recipe. But fathom for a moment usecases where it is critical where you need to do multiple things at once and they all need to happen or zero need to happen.

A good example of this would be bank accounts. If Bob is transferring $10,000 from his account to Alice's account, we need to subtract $10,000 from Bob's account and add $10,000 to Bob's. **Both** of those need to happen or **neither** of those needs to happen. We need it to be one _atomic_ transaction. Atomic in this case means indivisible, or rather it needs to happen as if it was one single query. If Bob _just_ loses $10,000 and Alice doesn't gain it the bank is committing theft and if Alice _just_ gains $10,000 and Bob doesn't lose it then the bank is losing money by paying Alice. If neither happens, they may be a bit mad that it's taking a long time to get the transfer done but no damage has been done and we can try again.

This is what transactions are for with PostgreSQL. It allows you to say "hey, PostgreSQL, do all of these things and if anything fails or doesn't work, then do none of it. Let's give it a shot with the recipes database.

You can really drive home how this works by opening a second terminal to the database and run a

```sql
BEGIN;

INSERT INTO ingredients (title, type) VALUES ('whiskey', 'other');
INSERT INTO ingredients (title, type) VALUES ('simple syrup', 'other');

INSERT INTO recipes (title, body) VALUES ('old fashioned', 'mmmmmmm old fashioned');

INSERT INTO recipe_ingredients
  (recipe_id, ingredient_id)
VALUES
  (
    (SELECT recipe_id FROM recipes where title='old fashioned'),
    (SELECT id FROM ingredients where title='whiskey')
  ),
  (
    (SELECT recipe_id FROM recipes where title='old fashioned'),
    (SELECT id FROM ingredients where title='simple syrup')
  );

COMMIT;
```

- `BEGIN` is how you _start_ a transaction. You're telling PostgreSQL "I'm giving a few queries now, don't run them until I say `COMMIT` and then run _all_ of them.
- If you `BEGIN` and decide you don't want to `COMMIT` you can run `ROLLBACK;` and it will not run any queries.
- If you want really prove a point to yourself, run the `BEGIN` and the first three `INSERT INTO` commands but don't run the last INSERT or the COMMIT. Open a second psql instance and try to query for "whiskey" in the ingredients table. You won't find it because we didn't `COMMIT` yet.
- `BEGIN` is short for `BEGIN WORK` or `BEGIN TRANSACTION`. Any of those work.

I used subqueries to get the IDs. You can use variables if you use plpgsql language and use `RETURNING INTO` and a variable name. Let's just show you what that could look like.

```sql
BEGIN WORK;

DO $$
DECLARE champagne INTEGER;
DECLARE orange_juice INTEGER;
DECLARE mimosa INTEGER;
BEGIN

INSERT INTO ingredients (title, type) VALUES ('champage', 'other') RETURNING id INTO champagne;
INSERT INTO ingredients (title, type) VALUES ('orange_juice', 'other') RETURNING id INTO orange_juice;

INSERT INTO recipes (title, body) VALUES ('mimosa', 'brunch anyone?') RETURNING recipe_id INTO mimosa;

INSERT INTO recipe_ingredients
  (recipe_id, ingredient_id)
VALUES
  (mimosa, champagne),
  (mimosa, orange_juice);

END $$;

COMMIT WORK;
```

- This is just to show you a different way to do it without subqueries. We're using the plpgpsql feature of variables. This does not work in normal SQL, we have to use the programming language.
- Notice the `BEGIN WORK` and `COMMIT WORK` are **outside** of the function. Postgres can't do transactions inside of functions. Other SQL databases can, just not Postgres.
- There's a `BEGIN WORK` for the transaction and a `BEGIN` for the function. They're different and do different things. I used `BEGIN WORK;` to make it very clear to you but you can use `BEGIN;` and it'd work just fine.
