+++
title = "RoadMap"
+++

Some of the items in the pgloader Road Map will only happen given some
financial contributions to the project. You are most welcome to participate
into the progress of pgloader!

The following projects are part of the pgloader Road Map, and after you've
bought a pgloader Moral Licence you are entitled to vote for them so see
them delivered in a time frame that makes sense for your migration projects.

  - New database sources

    - Oracle™ support
    - Sybase™ support
    - IBM DB2 support, via an ODBC driver
    
  - New SQL Objects support
  
    - View Definitions
    - Stored Procedures
    - Native PostgreSQL 10 Partitioned Tables, see [Issue #732](https://github.com/dimitri/pgloader/issues/732)
    - Materialize Views support for MS SQL, see [Issue #316](https://github.com/dimitri/pgloader/issues/316)

  - Quality and Delivery
  
    - More frequent releases
    - Binaries for all platforms, including Windows
    - Continuous Integration for database sources

## Support pgLoader development, be a Patron!

<center>
    <script src="https://gumroad.com/js/gumroad-embed.js"></script>
    <div class="gumroad-product-embed" data-gumroad-product-id="CjXn">
        <a href="https://gumroad.com/l/CjXn">Loading...</a>
    </div>
</center>

## Adding Support for a new Database 

pgloader has been developed with adding new source types in mind, so that
it's easy enough to add new drivers. Adding a new database source requires
the following steps:

  1. Integration of a Common Lisp driver for the database source
  
     In the case of Sybase, we're going to use the
     [FreeTDS](http://www.freetds.org) driver, that already has been
     integrated into pgloader in order to add support for MS SQL.
     
     In the case of Oracle™, we are considering
     [cl-oracle](https://github.com/archimag/cl-oracle). In many other
     cases, the ODBC driver support might be all we need.
     
  2. Catalog queries for the new database source
  
     The catalog queries in pgloader are integrated in SQL files, so that
     it's easy for non-Common-Lisp hackers to edit them. See for instance
     the SQL Server queries at
     [src/sources/mssql/sql](https://github.com/dimitri/pgloader/tree/master/src/sources/mssql/sql).
     
  3. Data Types default casting rules
  
     We then need to export the data in a way that PostgreSQL is happy
     about, and it sometimes requires more work that it should.
     
Once those 3 steps are implemented, we have a new pgloader data source.
That's pretty simple actually.

## Adding support for new SQL Objects

Currently pgloader only supports tables, indexes, some constraints (not
null, unique, primary keys, foreign keys) and sequences. With some more
advanced RDBMS, it might not be enough.

It is possible to add new SQL objects to pgloader. The main difficulty of
the task is going to decide how to best map those new objects to PostgreSQL
feature set.

For instance, Oracle Synonyms allow tables from one schema to be used in
another schema. That's because in Oracle, a schema belongs to a user. When
using PostgreSQL, we don't have that arbitrary limitation, and we don't need
synonyms as a result.

## Compilers, Transpilers

pgloader currently doesn't have a look at the SQL definition of objects, it
uses the catalog representation of them. So there's currently no SQL parser
in pgloader.

In order to support View Definitions and Stored Procedures, it will be
necessary to write SQL compilers that know how to transform SQL text from a
dialect to another. That's something I began having a look at in the
[plconvert](https://github.com/dimitri/plconvert) project.

So if you need to add support for View Definition in pgloader, which means
rewriting the view SQL definition in another SQL dialect, then let's talk
about it!

## Influence the Road Map

You want to add new items to the Road Map? You need existing items being
taken care of now? Then contribute to the road map!

There are two ways to contribute to pgloader Road Map. As it's a fully Open
Source project, you can of course implement the feature yourself and
contribute it to the project. To do that, [fork the project on
github](https://github.com/dimitri/pgloader) and get started, then submit a
*Pull Request*. As usual.

If you don't want to do that — maybe because you don't have enough time to
both hack pgloader and migrate your database — then contribute financially
to the project by subscribing the pgloader Patrons Membership as shown
above.

<center style="margin-bottom: 2em">
 <a class="btn" href="https://gum.co/CjXn" target="_blank">
    Become a pgloader Patron!
 </a> 
</center>

Then we will raise your missing feature's priority and assign someone to
implement it for you! Of course, we will need to be able to talk to you
about it and run tests that make sense to you. The best possible way for you
to ensure that our development meet your criteria is to provide us with a
testing environment and test cases.
