You did it!

Congratulations, you are well on your way to being an expert at SQL and PostgreSQL. Some final thoughts for you on what you learned today.

- _Most_ of what you learned today is applicable to \_any SQL database: MySQL, Oracle, SQLite, Microsoft SQL Server, etc. SQL is a general spec that all of these database then take and put their own flavor on. Many of these queries will work as-is on a different database. The rest have to be tweaked slightly to fit the other database's flavor. But all the principles you learned apply.
- This course focused exclusively on how to query a database and was generally focused from a programmer's perspective and _not_ an operations perspective. Notice we didn't talk about replication, configuring a server, deploying, backing up, replication, etc. This is a course for another time (and another teacher!) This was to teach you about basic querying skills.
- We also didn't cover ORMs for Node.js. In general I've had more problems than time saved by using ORMs. I always want to have access to the underlying SQL at the end of the day. I normally need that control.

We also did not talk a lot about execution order of queries whereas lots of other courses do talk about it. Why? When I learned SQL for the first time I found it _very_ confusing and I focused a lot on it when in reality 90% of the time it doesn't actually matter. Do you care if a `INNER JOIN` or a `WHERE` happens first? Normally no! Hence I left it out and as an exercise to you to explore more if you want to delve into it. Suffice to say, queries break down into a plan from PostgreSQL and then are executed in a particular order. In general you don't have to care. Only time we cared is with `WHERE` vs `HAVING` where HAVING has to happen last. [See this blog post][post] to have a more comprehensive overview.

Great job again! I hope you enjoyed the course! Please [tweet at me][tweet] what you thought and [give this repo a star][repo]!

[repo]: https://github.com/btholt/complete-intro-to-sql
[tweet]: https://twitter.com/hotlbt
