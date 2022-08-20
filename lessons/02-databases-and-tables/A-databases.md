Let's get started by making our very first database. For our first project we are going to be making a recipe website.

> If you haven't gotten your Docker container with Postgres 14 running in the previous lesson, please do that now.

Let's connect to the running database by running the following:

```bash
docker exec -u postgres -it pg psql
```

This should connects us to the running `pg` (which we named as such) container as the user `postgres` in an interactive session (via the `-it` flags) to run the bash command `psql`. If you were just running this locally, you could likely just run `psql` and it would work. Only thing you'd need to make sure you can auth correctly.

## psql commands

psql has a bunch of built in commands to help you navigate around. All of these are going to begin with `\`. Try `\?` to bring up the help list.

For now we're interested in what databases we have available to us. Try running `\l` to list all databases. You'll likely see the databases postgres, template0, and template1.

## Default databases

`template1` is what Postgres uses by default when you create a database. If you want your databases to have a default shape, you can modify template1 to suit that.

`template0` should never be modified. If your template1 gets out of whack, you can drop it and recreate from the fresh template0.

`postgres` exists for the purpose of a default database to connect to. We're actually connected to it by default since we didn't specify a database to connect to. You can technically could delete it but there's no reason to and a lot of tools and extensions do rely on it being there.

## Create your own

Okay let's create our own. Run this command in your database.

```sql
CREATE DATABASE recipeguru;
```

> Note if you kill your container, it will kill all the data with it and you will need to recreate everything if you do that. I do provide [this complete SQL file][sql-file] that has _everything_ for the recipeguru database but it's for the whole lesson so if you do need to restart it may be easier to go back and do the creation by hand.

A database is a collection of similar tables of data. For our recipe app we will store all our tables in one database. There's a lot of schools of thought of when to shove everything into one database versus when to decompose it all into various different databases. I generally think in terms of clients: if my app is going to be querying for the data, then I'll try to keep it in an app-oriented database. If I have another client that is going to be storing client analytics for my team to digest later, I'd probably put that in a separate database. It's a bit vague of when you should, but think in terms of "what if I had to scale these independently" sorts of terms.

Type `\c recipeguru` to connect to our database. We were previously connected to the `postgres` default database.

> You may notice that I use all capitals when I write SQL terminology e.g. CREATE DATABASE or SELECT or INSERT INTO. Why? It's not required and lots of people _never_ do the all caps thing so it's an opinion. I'm doing it for you because I think it makes it easier to say "this comes from SQL and this doesn't". You are free to decide what you think. In general in code I'll do the all caps but if I'm just messing around in psql I'll do lowercase.

[sql-file]: https://sql.holt.courses/recipes.sql
