---
title: "Node.js and PostgreSQL"
---

This is not a Node.js class so the expectation of your Node.js skills here are fairly low. No worries if you don't have a ton of experience with Node.js

We are going to be using the [pg][pg] package which is [the most common PostgreSQL client][trends] for Node.js. There are others but this is the one with the most direct access to the underlying queries which is what we want. Feel free to use an ORM in your projects but it's useful to know how the underlying PostgreSQL works.

Before I dump you into the code I wanted to give you a very brief tour of what to expect.

## Connecting to a database

pg has two ways to connect to a database: a client and a pool.

You can think of a client as an individual connection you open and then eventually close to your PostgreSQL database. When you call `connect` on a client, it handshakes with the server and opens a connection for you to start using. You eventually call `end` to end your session with a client.

A pool is a pool of clients. Opening and closing clients can be expensive if you're doing it a lot. Imagine you have a server that gets 1,000 requests per second and every one of those needs to open a line to a database. All that opening and closing of connections is a lot of overhead. A pool therefore holds onto connections. You basically just say "hey pool, query this" and if it has a spare connection open it will give it to you. If you don't then it will open another one. Pretty neat, right?

Okay, we're going to be using pools today since we're doing a web server. I'd really only use a client for a one-off script or if I'm doing transactions which aren't supported by pools (transactions are not covered in this course but it's if multiple queries _have_ to happen all at once or not all). Otherwise I'd stick to pools. One isn't any harder to use than the other.

So let's see how to connect to a database.

```javascript
const pg = require("pg");
const pool = new pg.Pool({
  user: "postgres",
  host: "localhost",
  database: "recipeguru",
  password: "lol",
  port: 5432,
});
```

- These are the default credentials combined with what I set up in the previous lessons. Make sure you're using the correct credentials.
- Make sure you started PostgreSQL via docker with the `-p 5432:5432` flag or PostgreSQL won't be exposed on your host system.
- Make sure your database is running too.

Once you've done this, you can now start making queries to PostgreSQL!

Let's write a query.

```javascript
const { rows } = await pool.query(`SELECT * FROM recipes`);
```

- `rows` will be the response of the query.
- There's lot of other metadata on these queries beyond just the rows. Feel free to `console.log` it or `debug` it to explore.
- pg will add the `;` at the end for you.

## Parameterization and SQL injection

Let's say you wanted to query for one specific ingredient by `id` and that id was passed in via an AJAX request.

```sql
SELECT * FROM ingredients WHERE id = <user input here>;
```

What's wrong with then doing then:

```javascript
const { id } = req.query;
const { rows } = await pool.query(`SELECT * FROM ingredients WHERE id=${id}`);
```

SQL injection. You're just raw passing in user data to a query and a user could fathomably put _anything_ in that id API request. What happens if a user made id equal to `1; DROP TABLE users; --`?

Oh no 😱

This is SQL injection and a very real issue with SQL in apps. It's why Wordpress apps are always under attack because they use MySQL behind the scenes. If you can find somewhere you can inject SQL you can get a lot of info out of an app.

So how do we avoid it? Sanitization of your inputs and parameterization of your queries.

```javascript
const { id } = req.query;

const { rows } = await pool.query(`SELECT * FROM ingredients WHERE id=$1`, [
  id,
]);
```

If you do this, pg will handle making sure that what goes into the query is safe to send to your database. Easy enough, right?

> If you need to do a a thing like `WHERE text ILIKE '%star wars%'` and you're using parameterized queryies, just put the `%` in the thing to be inserted, e.g. `('WHERE text ILIKE $1', ['%star wars%'])`

[pg]: https://node-postgres.com/
[trends]: https://npmtrends.com/knex-vs-objection-vs-pg-vs-sequelize-vs-typeorm
