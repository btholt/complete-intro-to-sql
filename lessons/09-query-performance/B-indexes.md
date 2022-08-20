## Identifying what to index

We've identified some slow queries and how to diagnose them as slow. We've identified what a index scan is versus what a sequential scan is. Here we're going to learn how to index things and when we want to do that.

First thing is, _don't index everything_. Indexes take space and do incur some overhead for PostgreSQL to decide if it's going to use (using what's called the query planner.) If you have data that's rarely accessed or frequently filtered out then don't index it!

So what do you index? Things you query frequently that have to filter out lots of rows.

## It's okay that sometimes that the planner doesn't use the index

Another thing to keep in mind is indexes are only useful when you're filtering data out or doing some sort of different sort. It all depends on what you want to do.

Think of it like a dictionary and the page numbers are like indexes you can query on. If you're looking for one word, 'defenestrate', it's easy to use the page numbers / index to look up that one query quickly. You don't have to scan every row / word on the As-Cs to find the word: you can skip until you're close.

However what if your goal is to read the dictionary cover to cover? What use are indexes are you for then? You wouldn't use the page numbers at all in this case (and neither would the planner.) Therefore the planner would still use sequential scan because adding an index just adds unnecessary overhead.

This isn't just for complete datasets either. Imagine you're reading the whole dataset _except_ the Xs. The X section is so short you'd probably just scan over them to get to the Ys. Same thing with the planner: it'll make a call sometimes to just scan over rows because adding an index would be too much overhead. Basically what I'm saying is that the planner is usually right even if it's unintuitive.

## Unique indexes

So we've seen and used one sort of index already: primary keys (which are normally sequentially increasing integers called `id` or similar.)

Unique and primary keys create indexes by default and you don't need to do anything else to get them. They do this because everytime you insert into a unique key it needs to go check if it already exists so it can enforce uniqueness, making it necessary to have an index.

Keep in mind you can also have uniqueness as a constraint across multiple columns. Imagine you stored address, city, and state as three columns. Multiple people could live on `1000 4th Street` across Seattle, San Francisco, Minneapolis, and Savannah, but there can only be one `1000 4th Ave, Seattle, WA`. Thereforce a unique constraint across multiple columns could be really useful. You'd do that with something like

```sql
CREATE TABLE american_addresses (
    id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    street_address int NOT NULL,
    city VARCHAR(255) NOT NULL,
    state VARCHAR(255) NOT NULL,
    UNIQUE (street_address, city, state)
);
```
