---
description: ""
---

Let's move on from our recipe database that we crafted. While before I was showing you how to set up schemas and how to write to a database, the next sections are going to be more focused on reading from a large dataset.

The database I've prepared for us to work on is [the Open Media Database][omdb]. This is a wonderful project to catalog movies into a useful database. As part of this they offer a [database dump][gh-omdb] that is explictly created for folks to learn PostgreSQL with.

I went ahead wrapped the above into a public container and [put it on Docker Hub][docker]. It's a pretty simple container, [see here to see the Dockerfile][gh-docker].

Let's run it. Run the following in your CLI.

```bash
docker run -e POSTGRES_PASSWORD=lol --name=sql -d -p 5432:5432 --rm btholt/complete-intro-to-sql
```

> You need to stop the recipe container for the above command to work since both will try to bind to port 5432. If you want both to run, you can do `5433:5432` instead of `5432:5432` to expose port 5433 on your local machine so there won't be a conflict.

Okay, now to connect to that container in psql, do:

```bash
docker exec -u postgres -it sql psql omdb
```

> The last omdb connects you directly to the omdb database instead of having to run `\c omdb` when you first connect

Okay, let's explore a bit.

- Run `\l` to see all database.
- Run `\d` to see all tables.

We can see there are a ton of tables which means there are a lot of relations going on.

Let's look at one of them. Run `\d movies` to see everything about movies. You can see all the columns and indexes (we're about to see more about indexes here in a bit.)

Before we start learning some more concepts, let's have some fun doing some queries:

## Which movie made the most money?

- Some movies have null revenues. Use `COALESCE(column_name, 0)` to make nulls into 0s.
- Won't need any joins here.
- `ORDER BY` is your friend, as is `DESC`

## How much revenue did the movies Keanu Reeves act in make?

- `COALESCE` will be useful again
- `casts` holds the links between `movies` and `people`
- Multiple joins necessary

## Which 5 people were in the movies that had the most revenue?

- Multiple joins here too

## Which 10 movies have the most keywords?

- `movie_keywords` contains the connections
- `categories` contains the names of the keywords
- Multiple joins necessary
- This database contains multiple languages of keywords. Don't worry about it.

## Which category is associated with the most movies

- Similar to the above query, just starting from a different place.

[omdb]: https://www.omdb.org
[gh-omdb]: https://github.com/credativ/omdb-postgresql
[docker]: https://hub.docker.com/r/btholt/complete-intro-to-sql
[gh-docker]: https://github.com/btholt/complete-intro-to-sql-container/blob/main/Dockerfile
