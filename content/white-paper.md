+++
title = "White Paper"
+++

<figure style="float: left; clear: right; display: block; width: 200px; margin-right: 1em;">
    <a href="/white-paper/">
        <img style="width:200px; height: 229px; border: 1px solid lightblue; box-shadow: 15px 0 20px -20px lightblue, -15px 0 20px -20px lightblue;"
               src="/img/MigratingToPostgreSQL-Cover.png">
    </a>
</figure>

The pgloader tool is meant to allow one to implement the ***Continuous
Migration*** project methodology when migrating to PostgreSQL. This
methodology is meant to reduce risks inherent to such complex projects.

After having been involved in many migration projects in the past, I decided
to publish a White Paper about this project methodology!

# Migrating to PostgreSQL, Tools and Methodology

<script async id="_ck_322615" src="https://forms.convertkit.com/322615?v=6">
</script>

The migration method proposed here is called ***Continuous Migration***.
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

The third step isn't always as easy to implement as it should be, and that's
why the [pgloader](https://pgloader.io) open source project exists: it
implements fully automated database migrations!

<figure style="float: right; clear: left; display: block; width: 200px; margin-left: 0em;">
    <a href="http://masteringpostgresql.com">
        <img style="width:200px; height: 229px;"
               src="/img/MasteringPostgreSQLinAppDev-Cover-th.png">
    </a>
</figure>

For your developers to be ready when it comes to making the most out of
PostgreSQL, consider reading [Mastering PostgreSQL in Application
Development](https://masteringpostgresql.com) which comes with an Enterprise
Edition that allows a team of up to 50 developers to share this great
resource.

