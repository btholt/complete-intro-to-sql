---
description: ""
---

You actually already know what constraints are and have used them not once but TWICE so far!

A constraint is just a constraint you put around a column. For example, NOT NULL is a constraint we've used for our primary keys. You always need a primary key.

UNIQUE is another constraint that dictates that this column must be unique amongst all other columns. Ever wonder how an app can tell you so quickly if an email or a user name is taken so quickly? They likely use a UNIQUE constraint on those two columns. They should. PRIMARY is another such constraint.

We also added a constraint on recipe_ingredients that every row must have a unique combination of recipe_id and ingredient_id. We could have added an additional primary, incrementing, unique ID but what would we use that for? To me at this moment it doesn't serve a purpose so we can just use the other two as a unique key. This is common for these sorts of many-to-many connecting tables.

Foreign keys are a type of constraint as well. They enforce consistency amongst tables.

## CHECK

CHECK allows you to set conditions on the data. You can use it for enforcing enumerated types (e.g. 'male', 'female', 'nonbinary', 'other' for gender.) Or you could have it test that a zipcode is five characters (in the USA.) Or that an age isn't a negative number.

We actually have a very good use case for one: our type in the ingredients. It can be 'fruit', 'vegetable', 'meat', or 'other'. We could write a CHECK constraint that enforces that.

```sql
ALTER TABLE ingredients
ADD CONSTRAINT type_enums
CHECK
  (type IN ('meat','fruit','vegetable','other'));
```

This will add a constraint to the existing table. You can also create these constraints when you create the table (like we did with the foreign keys as well as unique, not null, and primary.)

Just for fun, let's try to break it.

```sql
INSERT INTO
  ingredients
  (title, image, type)
VALUES
  ('lol', 'wat.svg', 'obviously not a type');
```
