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

# Database Migration

Migrating the data should be easy. After all, your source database is
already a relational database server and implements the same SQL basics as
any other one: relations, tables, views, and the expected column data types,
or attribute and attribute domains.

It is actually quite impressive to realize how many differences exist
between different technologies when it comes to implementing data types,
default values, and other behaviors.

When using pgloader, all the knowledge when it comes to those differences is
integrated into an open source product.

## General Migration Behavior

[pgloader](https://pgloader.io) follows those steps when migrating a whole
database to PostgreSQL:

  1. Fetch meta data and catalogs
  
     pgloader fetches the source database meta data by querying the live
     database catalogs. Retrieving structured data makes it easier to
     process the complex level of information. Catalog queries that are used
     can be seen in the pgloader source code at GitHub.
     
     In cases in which a database schema *merge* is required — that's called
     the ORM compatibility mode, where the schema is created by a tool other
     than pgloader — then pgloader also fetches the target PostgreSQL
     metadata and compares it to the source catalogs.
     
     The information retrieved by pgloader is handled internally in its own
     in-memory catalog format, and then used throughout the migration tasks.
     It is possible to implement some *catalog mapping* in these internal
     pgloader catalogs, as seen later in this chapter.
     
  2. Prepare the target PostgreSQL database
  
     Unless the database has already been created by another tool, pgloader
     creates the target PostgreSQL database schema by translating the source
     catalogs into PostgreSQL specific catalogs.
     
     This steps involves *casting rules* and the advanced command language
     included in pgloader allows the user to define custom casting rules.
     
     The casting rules are applied to the schema definition (column types,
     or attribute domains) and default values.
  
  3. Copy table data using PostgreSQL copy protocol
  
     For each table fetched in the first step, pgloader starts two threads:
     
       - The reader thread issues a `select` statement that retrieves all
         the data from the source database, sometimes already with some data
         processing.
         
       - The writer thread receives the data read from the other thread
         thanks to a shared queue, and batches the data to be sent over to
         PostgreSQL.
         
         While arranging a batch of data, pgloader also applies
         transformation functions to the data. The transformations applied
         depend on the casting rules. Examples include transforming a
         *tinyint* value into a *Boolean* value, or replacing date time
         entries `0000-00-00 00:00:00` with NULL values.

     It's possible to setup how many threads are allowed to be active at the
     same time on a pgloader run, and the data migration timing is
     influenced by that setup.
     
  3. Create the indexes in parallel
     
     For each table, as soon as the entire data set has made it to
     PostgreSQL, all the indexes defined on the table are created in
     parallel. The reason why pgloader creates all the indexes in parallel
     is PostgreSQL's implementation of
     [synchronize_seqscans](https://www.postgresql.org/docs/current/static/runtime-config-compatible.html#GUC-SYNCHRONIZE-SEQSCANS):
     
     > This allows sequential scans of large tables to synchronize with each
     > other, so that concurrent scans read the same block at about the same
     > time and hence share the I/O workload. When this is enabled, a scan
     > might start in the middle of the table and then “wrap around” the end
     > to cover all rows, so as to synchronize with the activity of scans
     > already in progress. This can result in unpredictable changes in the
     > row ordering returned by queries that have no ORDER BY clause.
     > Setting this parameter to off ensures the pre-8.3 behavior in which a
     > sequential scan always starts from the beginning of the table. The
     > default is on.
     
     For a large table, having to scan it sequentially only once to create
     many indexes is a big win of course. Actualy, it's still a win with
     smaller tables.
     
     A *primary key* index is created with the `ALTER TABLE` command though,
     and PostgreSQL issues an *exclusive lock*, which prevents those indexes
     from being created in parallel with the other ones. That's why pgloader
     creates primary key indexes in two steps. In this step, pgloader
     creates a unique index over not-null attributes.

  4. Complete the target PostgreSQL database
     
     Once all the data is transfered and all indexes are created, pgloader
     completes the PostgreSQL database schema by installing the constraints
     and comments.
     
     Constraints include turning the candidate unique not-null indexes into
     primary keys, then installing foreign keys as defined in the source
     database schema.

Once all those steps are done, your database has been converted to
PostgresQL and it is ready to use!

As explained in the previous part about ***Continuous Migration***, the main
idea behind pgloader is to implement fully automated database migrations so
that you can run this as a nightly task for the whole duration of your
migration project.

# Sample Output

It's possible to run a whole database migration with a single command line,
using only pgloader defaults, like this:

~~~
$ pgloader mysql://root@localhost/f1db pgsql:///f1db
~~~

Here's a sample output of that command:

~~~
               table name     errors       rows      bytes      total time
-------------------------  ---------  ---------  ---------  --------------
          fetch meta data          0         33                     0.231s
           Create Schemas          0          0                     0.009s
         Create SQL Types          0          0                     0.007s
            Create tables          0         26                     0.095s
           Set Table OIDs          0         13                     0.005s
-------------------------  ---------  ---------  ---------  --------------
  f1db.constructorresults          0      11142   186.2 kB          0.123s
            f1db.circuits          0         73     8.5 kB          0.020s
        f1db.constructors          0        208    15.0 kB          0.026s
             f1db.drivers          0        842    79.8 kB          0.059s
            f1db.laptimes          0     426633    11.2 MB          2.760s
f1db.constructorstandings          0      11896   249.3 kB          0.184s
     f1db.driverstandings          0      31726   719.1 kB          0.533s
            f1db.pitstops          0       6251   209.6 kB          0.234s
               f1db.races          0        997   100.6 kB          0.200s
             f1db.seasons          0         69     3.9 kB          0.194s
          f1db.qualifying          0       7516   286.4 kB          0.146s
             f1db.results          0      23777     1.3 MB          0.364s
              f1db.status          0        134     1.7 kB          0.008s
-------------------------  ---------  ---------  ---------  --------------
  COPY Threads Completion          0          4                     3.193s
           Create Indexes          0         20                     2.033s
   Index Build Completion          0         20                     1.274s
          Reset Sequences          0         10                     0.061s
             Primary Keys          0         13                     0.014s
      Create Foreign Keys          0          0                     0.000s
          Create Triggers          0          0                     0.000s
          Set Search Path          0          1                     0.002s
         Install Comments          0          0                     0.000s
-------------------------  ---------  ---------  ---------  --------------
        Total import time          ✓     521264    14.3 MB          6.577s
~~~

The output clearly shows three sections: prepare the target database schema,
transfer the data and complete the PostgreSQL database schema. Performance
and timning will vary depending on hardware used. This sample is using a
laptop optimized for traveling light.

As a result, don't read too much into the numbers shown here. Best practices
for doing the database migration include having different source hardware
and target machines in order to maximize the disk IO capacity, and also a
high bandwitdth network in between the servers. Using the same disk both as
a source for reading and a target for writing is quite bad in term of
performance.

# Catalog Mapping

It is possible to change some catalog entries on the fly when doing a
database migration. Supported changes include:

  - Limiting tables to take care of,
  - Migrating from one schema name to another one,
  - Renaming tables.

It is also possible (only with MySQL as a source at the moment) to migrate
data from a view definition rather than a table, allowing schema redesign on
the fly.

# Data Type Casting Rules

In order for the database migration to be fully automated, pgloader allows
users to define their own casting rules. Here's a `sample.load` command file
implementing this idea:

~~~
load database
     from mysql://root@unix:/tmp/mysql.sock:3306/pgloader
     into postgresql://dim@localhost/pgloader

 alter schema 'pgloader' rename to 'mysql'

 CAST column base64.id to uuid drop typemod drop not null,
      column base64.data to jsonb using base64-decode,

      type decimal when (and (= 18 precision) (= 6 scale))
        to "double precision" drop typemod

 before load do $$ create schema if not exists mysql; $$;
~~~

Custom casting rules may target either specific columns in your source
database schema, or a whole type definition.

# pgloader Features

Many users already use pgloader happily in their production environment and
are quite satisfied with it. The software comes with a long list of features
and is well documented at <http://pgloader.readthedocs.io/en/latest/>.

# Supported RDBMS

pgloader already has support for migrating the following RDBMS in a fully
automated way:

  - DBF
  - SQLite
  - MySQL
  - MS SQL Server

More sources for migrations are possible, such as Oracle™, Sybase™ or IBM
Db2, see the [pgloader Road Map](/roadmap/) document for details.
