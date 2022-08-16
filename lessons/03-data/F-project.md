---
description: ""
---

Project time!

Let's do a Node.js project. Hopefully the Node.js part is minimal but it will require JavaScript skills.

[Go clone this repo from GitHub][project]. In this repo you will find our two big projects, one for the recipes project and one for the movies project which we will do later. You'll find a starter project and a completed project. The latter is for you to use to compare my answer to use. Like coding, there are many correct ways to write SQL and almost always trade offs. The goal is learning, so if it helps you to look at the answer, do it! There's no cheating here, only learning. Don't cheat yourself of learning.

We're going to go to the ingredients page first (we're doing recipes next.) Go to the ingredients/api.js part of the app and start working on the answers.

> - Make sure your Postgres Docker database is running by running `docker ps` or looking at the Docker GUI
> - Make sure that your database is exposing port 5432. You can see that in the `docker ps` output or in the GUI.
> - Make sure you're connecting to the correct database. If you didn't create a new database, the data you created will be in the default `postgres` database. If you followed my instructions, the data will be in the `foodguru` database.
> - If your database is missing the data, go back to [the INSERTs lesson][inserts] and copy the long query there and rerun it in your database.

## Your project

1. Make sure your database is connecting correctly with the credential you're supplying
1. Make the various ingredient carousels work. This will involve making selecting the right type.
1. Make deletes work.
1. Make the full text search work.
1. Make the pagination work for search. You can use OFFSET here. Doing it with IDs is more correct, but a stretch goal here.

## Tips

- You shouldn't have to modify the HTML, CSS, or client.js file (unless you do the pagination by ID in which you'd need to modify client.js)
- You shouldn't have to modify any Node.js besides the function bodies of the handlers, and most of the skeleton of that is done for you. You really only need to query Postgres.
- It's called `pool` in JS because your server will pool connections to Postgres. It's expensive to open and close connections to the database so the pool will keep connections open to speed up that process.
- We're using the `pg` module in Node.js for Postgres. It's by far the most popular. [See documentation here][pg].
- Any time you delete something you can go back and rerun the [big INSERT query from the inserts lesson][inserts].

## We need a window function

Okay, I need you to add one little thing to your query to get this all to work correctly, but we're not that far in the lesson yet.

Try this query in your CLI.

```sql
SELECT id, title, COUNT(*) OVER ()::INT AS total_count FROM ingredients;
```

The `COUNT(*) OVER ()::INT AS total_count` is going to return the total count of all rows in this query as an integer and return it as total_count. Please pass this total_count to the frontend so pagination will work. We'll go over this later, but please add that to your query so the pagination on the search results works.

[project]: https://github.com/btholt/sql-apps
[pg]: https://node-postgres.com/
[inserts]: /lessons/data/inserts
