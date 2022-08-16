TODO: move to movie section

A little bonus fun puzzle for you.

You can join tables to themselves. Why would you ever want to do that?

Let's look at category_names. category_names contains all the names of categories and keywords and it contains multiple languages. The most common languages in our database are English and German. What if I wanted to find words that had German translations but not English? That way I could assign a translator to go translate that so we could have parity. This is a job for a table joined to itself.

```sql
SELECT
  c1.category_id, c1.language AS de_lang, c1.name as de_name, c2.language AS en_lang, c2.name AS en_name
FROM
  category_names c1

LEFT JOIN
  category_names c2
ON
  c1.category_id = c2.category_id
AND
  c2.language = 'en'

WHERE
  c2.language IS NULL
AND
  c1.language = 'de'

LIMIT 50;
```

- You treat the self join just like any other, just give them different names in your query. I called the "left" side of my equation `c1` and the "right" `c2`.
- I used the LEFT JOIN because I want to have a row for _any_ German category, whether it has a English row to join to or not.
- Then we use WHERE to limit c1 to de and to check to see where the `en` is null (which is left in because of the LEFT JOIN)
- If you set `IS NULL` to `IS NOT NULL` you'll see German and English translations in the same line.

This is a bit of an advance use case and to be quite clear, I took a while to tinker with this to get it to be just what I wanted. Most people don't get it right the first time, just like programming in general.
