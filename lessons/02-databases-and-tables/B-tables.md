Let's create our first table, the `ingredients` table.

```sql
CREATE TABLE ingredients (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  title VARCHAR ( 255 ) UNIQUE NOT NULL
);
```

> INT, INT4 or INTEGER are the same thing. Feel free to use what suits you. Similarly DEC, FIXED, and DECIMAL are the same thing.

This will create our first table for us to start using. We will get into data types during the course but just know now that a VARCHAR is a string of max length 255 characters and an INTEGER is, well, an integer.

To see the table you created, run `\d` in your psql instance to see it and the the sequence that you created. The sequence stores the `id` counter.

We now have a table. A table is the actual repository of data. Think of a database like a folder and a table like a spreadsheet. You can have many spreadsheets in a folder. Same with tables.

We now have a table, ingredients. Our table has two fields in it, an incrementing ID and a string that is the the title of the ingredients. You can think of fields like columns in a spreadsheet.

A table contains records. A record can be thought of as a row in a spreadsheet. Every time we insert a new record into a table, we're adding another row to our spreadsheet.

> The spreadsheet analogy isn't just theoretical. [You can essentially use Google Sheets as a database][sheets] (appropriate for small, infrequent use.)

Let's add a record to our table.

```sql
INSERT INTO ingredients (title) VALUES ('bell pepper');
```

> We'll get more into these queries in a sec. For now just roll with it.

This adds one row with a title of bell pepper. Where is the id? Since we made it `GENERATED ALWAYS AS IDENTITY` it gets created automatically. Since this is the first item in our database, its ID will be `1`. As you have likely guessed already, the next item in the table will be `2`.

> Previously PostgreSQL used a field type called SERIAL to describe the serially incrementing IDs. The GENERATED AS ALWAYS syntax is newer, more compliant to the generic SQL spec, and works better. Always use it over SERIAL.

Let's see the record.

```sql
SELECT * FROM ingredients;
```

> We'll dive into SELECTs in a bit.

You should see something like

```plaintext
 id |    title
----+-------------
  1 | bell pepper
```

Amazing! We now have a table with a record in it.

## Dropping a table

What if we messed up and we didn't want an ingredients table?

```sql
DROP TABLE ingredients;
```

Pretty simple, right? That's it! Do be careful with this command. Like `rm` in bash, it's not one you can recover from. Once a table is dropped, it is dropped.

## ALTER TABLE

Let's recreate our table.

```sql
CREATE TABLE ingredients (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  title VARCHAR ( 255 ) UNIQUE NOT NULL
);
```

Okay so now we have a table again. What happens if we wanted to add a third field to our table? Let's add an `image` field that will point to a URL of an image of the item.

```sql
ALTER TABLE ingredients ADD COLUMN image VARCHAR ( 255 );
```

Likewise we can drop it too:

```sql
ALTER TABLE ingredients DROP COLUMN image;
```

There's a lot of ways to alter a table. You can make it UNIQUE like we did or NOT NULL. You can also change the data type. For now let's add back our extra column.

```sql
ALTER TABLE ingredients
ADD COLUMN image VARCHAR ( 255 ),
ADD COLUMN type VARCHAR ( 50 ) NOT NULL DEFAULT 'vegetable';
```

This is how you add multiple records! Just add multiple "ADD COLUMN"s. As you may have guessed, you can do multiple different types of operations if you need to in one atomic transaction.

> Specifying a DEFAULT when using a NOT NULL constraint will prevent errors if the column has existing null values.

## Data types

There are so many data types in PostgreSQL that we won't get close to covering them all. [Have a peek here from the PostgreSQL docs][types]

[sheets]: https://www.npmjs.com/package/google-spreadsheet
[types]: https://www.postgresql.org/docs/current/datatype.html
