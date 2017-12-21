+++
date = "2017-12-21"
title = "About"
+++

[pgloader](https://github.com/dimitri/pgloader) loads data into PostgreSQL.
It knows how to load data from several kinds of *source*:

  1. Files
  2. Live Databases

Let's dive into those two different behaviours of pgloader, starting with
its support for migrating a whole database from a connection string.

# Database Migration

pgloader is able to connect to an existing database hosted in one of MySQL,
MariaDB, SQLite or Microsoft SQL Server instance, and *migrate* this
database to PostgreSQL. It does so all automatically, following simple
steps:

  1. Check database connection strings
  
  2. Fetch metadata from the source database
  
     That is done by querying the source database catalogs, often found in
     *information_schema* and the like. Supported objects include:
  
       - tables, with attribute names, data types, and default values
       - constraints, including primary keys and foreign keys
       - extra indexes, including on-top of geometry columns, or full text
         indexes
       - extra information such as *auto_increment*
       - other non-standard behaviors such as *on update current_timestamp*
         which needs to be converted to a *before trigger*

  3. Prepare the PostgreSQL target database
  
     This step supports preparing from scratch, possibly issuing *DROP*
     commands on existing objects, in order to be able to run the migration
     again as many times as necessary.
     
     This step also supports re-using an existing database definition, so as
     to be compatible with an ORM that would need to create the target
     schema itself.
     
  4. COPY the data from the source database over to the PostgreSQL database,
     transforming it on the fly when needed. 
     
     For instance, we're using the Julian Calendar, which goes from 1 B.C.
     to 1 A.D. There's no [Year
     Zero](https://en.wikipedia.org/wiki/Year_zero) in this calendar. Yet
     some RDBMS allow year zero in their *datetime* fields. pgloader
     converts those `0000-00-00` entries to *NULL* automatically, by default.
     
  5. Create all the Indexes in parallel
  
     As soon as a table's data COPY is done, pgloader creates all the
     indexes that belong to this table in parallel, in order to benefit from
     *synchronize_seqscans*, an awesome feature that made it to PostgreSQL
     8.3.
     
  6. Complete the PostgreSQL database
  
     Once all the tables and indexes have been created, then pgloader
     *upgrades* the UNIQUE indexes in PRIMARY KEYS when that's necessary,
     and then installs the Foreign Keys and other table constraints. Also
     supported triggers. And then resets the sequences to match with the
     content of the tables.

This behaviour allows for a fully automated database migration in a single
command line. Also, the database migration is repeatable: you can do it as
many times as you want. See the [Continuous
Migration](/blog/continous-migration/) article to understand why you want to
be using that feature!

# Loading data from Files

You might have to process huge CSV files from time to time. For example if
you're in the *telco* business, those might be *Call Detail Records*. When
using PostgreSQL the best way to load CSV files is the COPY command and
protocol, as read in the article entitled [Faster bulk loading in Postgres
with
copy](https://www.citusdata.com/blog/2017/11/08/faster-bulk-loading-in-postgresql-with-copy/)
from Craig Kerstiens.

pgloader implements a *retry policy* around the data it loads, so that if
your COPY transaction fails to load all the data, the rejected data is
separated away from the rest of the file, and the data that is accepted by
PostgreSQL makes it to the database server.

As simple way to describe pgloader is to say that it implement a
retry-wrapper on-top of the PostgreSQL COPY command.
