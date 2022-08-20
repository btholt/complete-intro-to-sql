Okay, so we've identified that we frequently query movies by exact names and this is a query we want to support. It's a big table (175721 rows) and therefore a sequential scan on this is _pricy_. So how we go about doing that?

```sql
EXPLAIN ANALYZE SELECT * FROM movies WHERE name='Tron Legacy';

CREATE INDEX idx_name ON movies(name);

EXPLAIN ANALYZE SELECT * FROM movies WHERE name='Tron Legacy';
```

Check out the difference there. On my machine I'm seeing a difference between a cost of `4929.51` and `12.30` for the total cost. _Wild_, right?

Do note I'm also seeing a difference in the startup cost. A sequential scan has a startup cost `0.00` or at least very, very low and the cost of the index for me is `4.44`. This is why sometimes using an index isn't worth it for the planner: using an index isn't _free_. You incur overhead to use it. Think of it doing something like memoization in your code. You don't memoize everything because frequently it buys you nothing with lots of additional code and overhead. Similar idea here.

## B-tree versus others

When you say `CREATE INDEX` you are implicitly saying you want a b-tree index. A [b-tree][btree] is a data structure that is specifically easy to search.

I teach a lesson on [Frontend Masters][fem] on how to do AVL trees which are a simplification of b-trees if you want to get into how a self-balancing tree works.

B-trees are compact, fairly simple data structures that are suited to general indexes. However there are a few more to be aware of. We're about to talk about GIN indexes next so let's cover the ones first we're not going to be talking about.

- Hash - Useful for when you're doing strict equality checks and need it fast and in memory. Can't handle anything other than `=` checks.
- GiST - Can also be used for full text search but ends up with false positives sometimes so GIN is frequently preferred.
- SP-GiST - Another form of GiST. Both GiST and SP-GiST offer a variety of different searchable structures and are much more special-use-case. I've never had to use either. SP-GiST is most useful for clustering types of search and "nearest neighbor" sorts of things.
- BRIN - BRIN is most useful for extremely large tables where maintaing a b-tree isn't feasible. BRIN is smaller and more compact and therefore makes larger table indexing more feasible.

[This page from the docs about index types][indexes] is helpful to glance at.

[indexes]: https://www.postgresql.org/docs/current/indexes-types.html
