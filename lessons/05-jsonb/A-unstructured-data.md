---
description: ""
---

Have you heard of [MongoDB][mongodb]? Scott Moss has a great course on it on Frontend Masters. It's a document-oriented database (as opposed to a relational database like PostgreSQL.) It really excels at data that is _unstructured_. Unstructured data means it's really hard to say ahead of time what your data is going to look like. It can be things like "I don't know how my data schema is going to evolve" to "I don't know how many things is going to have."

Take our photos table for example. We don't know how many photos each recipe is going to have. They each could have 0 photos or 30 photos. Both of those are acceptable and correct. MongoDB used to be the only\* (yes, others did it, but MongoDB was the most used) way to handle that well.

Then Postgres introduced the JSON (and then subsequently the JSONB) data type that allows PostgreSQL to handle these unbounded schemas, similar to how MongoDB does. Essentially the JSONB datatype lets you stick a JSON object into a column and then later we can use specific language to query that object.

## hstore

A little note on hstore, a data type that we won't be covering today. It's a key value store (think memcache or Redis) data type for PostgreSQL. These days it's far less useful because you can get basically all the functionality from JSONB.

## JSON vs JSONB

For almost everything we're going to show here you could use either. If you want a one liner: always use JSONB, in almost every case it's better. The JSON datatype is a text field that validates it's valid JSON, and that's it. It doesn't do any compression, doesn't validate any of the interior values datatypes, and a few other benefits. The B in JSONB literally stands for "better".

[Here's a great blog post on it from Citus Data][citus]. Heres's a quote on what they recommend:

> - JSONB - In most cases
> - JSON - If you’re just processing logs, don’t often need to query, and use as more of an audit trail
> - hstore - Can work fine for text based key-value looks, but in general JSONB can still work great here

[mongodb]: https://frontendmasters.com/courses/mongodb/
[citus]: https://www.citusdata.com/blog/2016/07/14/choosing-nosql-hstore-json-jsonb/
