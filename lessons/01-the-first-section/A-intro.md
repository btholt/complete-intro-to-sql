---
title: "Introduction"
description: "The introduction to this course."
---

## Course Outline

- Getting Postgres running via Docker
- What is a database?
- Why Postgres?
- What is SQL?
- Creation of a table
- SQL
  - Reading
    - SELECT
    - JOIN
    - UNION
    - EXPLAIN / Query Performance
    - GROUP BY
    - ORDER BY
    - DISTINCT
    - LIMIT
  - Writing
    - INSERT
    - UPDATE
    - UPSERT
    - DELETE
- Sub Queries
- Meta-querying your databases and tables
- Indexes
- Constraints
- JSONB
- Triggers
- Stored Procedures
- Replication
- Full text search
- Use with Node.js
- Sub queries
- Structuring your data
- The psql CLI
- data types
- pgadmin
- Views
- Materialized Views
- Window Functions
- Aggregation
- Foreign keys
- Configuration
- Debugging
- Extensions
  - PostGIS
  - hstore
- Backing up Postgres
- Security
  - Roles
  - Users

## Narrative

- A Recipes Website
  - Ingredients Table
    - A page of just the available ingredients
    - Basic table creation
      - INSERT
      - SELECT
      - UPDATE
  - Recipes Table
    - Foreign keys and constraints
    - Joins and unions
    - JSONB
  - Create and insert recipes
    - Safe inserts
    - Parameterization
    - SQL Injection
  - JSONB
    - Tags
    - Querying JSON
  - Triggers, Functions, and Procedures
    - Function: get all available types or tags
    - Procedure: log to table added ingredients
    - Trigger: new ingredient added
- A Movies Website
  - Expensive Queries
    - EXPLAIN
  - Indexes
  - Full text search
  - Subqueries
  - Views
    - Most popular actors
    - Materialized views vs views
  - Window functions / aggregation
    - Average / sum revenue across several cuts of movies
    - OVER / PARTITION BY

[twitter]: https://twitter.com/holtbt
[fem]: https://www.frontendmasters.com
