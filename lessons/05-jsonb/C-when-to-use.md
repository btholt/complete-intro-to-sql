I use JSONB a lot of PostgreSQL. I have a long history of using MongoDB and therefore my mindset fits well with the document oriented structures of it. JSONB does an amazing job of slotting into that in the right places.

For any record that is going to be present and finite, I always elect to make it a normal column. Then if I have any 1-to-many relationships like we did with photos or tags, I'll use JSONB. Because one photo only belongs to one recipe, this would have been perfect to model as a JSONB field.

Tags are a bit more murky: in theory we _are_ sharing tags across recipes (implying a many-to-many relationship like ingredients). If I decide to rename the "desserts" tag to "sweets", I'd have to go update that on every JSONB field that used that. Better yet here, we could have a table of tags that IDs and names, and then the JSONB field of every recipe could refer to IDs. Best of both worlds. Great way to model many-to-many relationships without the third table.

So why did we spend all this time learning how to model data "the old school" way with many tables?

1. Much of the data you'll encounter in your job is still modeled this way.
1. I'm not a performance expert but JOINs and JSONB queries have different performance profiles. It's good to know both in case you do need to model your data one way or the other.
1. I wanted to teach you JOINs

In general I make ample use of JSONB because it closely mirrors how a programmer thinks about problems. Just keep in mind you can get better performance almost always as a normal column. Use columns always and then use JSONB when you have unstructured or unbounded data.
