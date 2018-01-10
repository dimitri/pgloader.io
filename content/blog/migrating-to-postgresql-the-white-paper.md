---
author: "Dimitri Fontaine"
date: 2018-01-10
title: Migrating to PostgreSQL, the White Paper
best: false
---

After having been involved in many migration projects in the last 10 years,
I decided to publish the following [White Paper](/white-paper) in order to
share my learnings.

The paper is titled *Migrating to PostgreSQL, Tools and Methodology* and
proposed the **Continuous Migration** approach. Get the book now:

<script async id="_ck_322615" src="https://forms.convertkit.com/322615?v=6">
</script>

Continuous Migration makes it easy to make incremental progress over a
period of time, and also to pause and resume the migration work later on,
should you need to do that. The method is pretty simple — just follow those
steps:

  1. Setup your target PostgreSQL architecture
  2. Fork a _Continuous Integration_ environment that uses PostgreSQL
  3. Migrate the data over and over again every night, from your current
     production RDBMS
  4. As soon as the CI is all green using PostgreSQL, schedule the D-day
  5. Migrate without any suprises… and enjoy!

This method makes it possible to break down a huge migration effort into
smaller chunks, and also to pause and resume the project if need be. It also
ensures that your migration process is well understood and handled by your
team, drastically limiting the number of surprises you may otherwise
encounter on migration D-day.
