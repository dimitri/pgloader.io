---
author: "Dimitri Fontaine"
date: 2017-12-21
title: Continuous Migration
best: false
---

## From your current RDBMS to PostgreSQL

A migration project may take a little more than a week-end, so you need to
consider it as a real project. Follow this methodology and it's all going to
be fine, I promise:

When migrating from MySQL to PostgreSQL, follow those steps:

  1. Setup your target PostgreSQL Architecture
  2. Fork a Continuous Integration environment that uses PostgreSQL
  3. Migrate the data over and over again every night, from production
  4. As soon as the CI is all green using PostgreSQL, schedule the D-Day
  5. Migrate without suprise and enjoy!
  
# PostgreSQL Architecture

That needs to be done first. Setup your *High Availability* before doing
anything else. The goal is for both the service and the data to be highly
available, so first setup a fully automated *backup and restore* solution.

## Highly Available Data: Automated Recevery

For that, use either [pgbarman](http://www.pgbarman.org) or
[pgbackrest](http://pgbackrest.org), or maybe
[WAL-e](https://github.com/wal-e/wal-e) if you want to use Amazon S3.
Consider its replacement [WAL-G](https://github.com/wal-g/wal-g) too.

Don't roll it yourself. It's really easy to do it wrong, and what you want
is an all automated **recovery** solution. Go for that.

## Highly Available Data: Standby Servers

Once you have an *automated recovery* solution in place, you might want to
reduce the possible down-time by having a *stanby server* always ready to
take over.

If you want to understand all the details about the setup, you can read the
whole PostgreSQL documentation about [High Availability, Load Balancing, and
Replication](https://www.postgresql.org/docs/current/static/high-availability.html)
and then about [Logical
Replication](https://www.postgresql.org/docs/current/static/logical-replication.html).

The PostgreSQL documentation best in class, and patches that add or modify
PostgreSQL features are only accepted when they also update all the impacted
documentation. Which means that our documentation is always uptodate and
trustworthy. That's why you don't see much comments in there. If any.

If you're not sure about what to do now, setup a PostgreSQL Hot Standby
physical replica by following the steps at [Hot
Standby](https://wiki.postgresql.org/wiki/Hot_Standby). It looks more
complex than it is. All you need to do is :

  1. check your `postgresql.conf` to allow for replication
  2. open replication privileges on the network in `pg_hba.conf`
  3. use `pg_basebackup` to have a remote copy of your *Primary* data
  4. start your *Replica* with a setup that connects to the *Primary*

That's it really.

## Load Balancing and Fancy Architectures

Of course it's possible to setup more complex PostgreSQL Architectures.
Starting with PostgreSQL 10 you can use *Logical Replication*. It's easy to
setup, as seen in the [Logical Replication Quick
Setup](https://www.postgresql.org/docs/10/static/logical-replication-quick-setup.html)
part of the PostgreSQL documentation.

Do you need such a setup to get started migrating your current application
to PostgreSQL tho? Maybe not, your call.

# Continuous Integration, Continuous Delivery

That's how we do it now right? Your whole *testing and delivery pipeline* is
automated using something like a Jenkins or a Travis setup, or something
equivalent. Or even something better. Well then, do the same thing for your
migration project.

So we now are going to setup ***Continuous Migration*** to back you up for
the duration of the migration project.

# Nightly Data Migration

Chances are that once your data migration script is tweaked for all the data
you've seen through, some new data is going to show up in production that
will defeat your script.

To avoid data related surprises on the D-Day, just run the whole data
migration script for the production's data every night, for the whole
duration of the project. You will have such a track record of catering with
new data that you will fear no surprises. In a migration project, surprises
seldom are the good ones.

> _“If it wasn't for bad luck, I wouldn't have no luck at all.”_
>
> Albert King, Born Under a Bad Sign

# Porting the Code from MySQL to PostgreSQL

Now that you have a CI/CD environment fresh with yesterday's production data
every morning, it's time to rewrite those SQL queries for PostgreSQL.
Depending on the RDBMS your are migrating from, the differences in the SQL
engines are going to be either mainly syntactic sugar, or sometimes whole
missing features.

<figure style="float: right; clear: left; display: block; width: 200px; margin-left: 0em;">
    <a href="http://masteringpostgresql.com">
        <img style="width:200px; height: 229px;"
               src="/img/MasteringPostgreSQLinAppDev-Cover-th.png">
    </a>
</figure>

Take your time and try to understand how to do things in PostgreSQL if you
want to *migrate* your application rather than just *porting* it, that is,
making so that it runs the same feature set on-top of PostgreSQL rather than
your previous choice.

The book [Mastering PostgreSQL in Application
Development](https://masteringpostgresql.com) is a good companion to learn
how to best use PostgreSQL and its advanced SQL feature set.

# Continuous Migration

In conclusion, the idea of *Continuous Migration* is that you setup a target
production environment for your PostgreSQL based architecture first, and
then migrate over your current production data to that new environment in a
nightly basis.

Now that can run a *fork* of your CI/CD pipeline that targets that new
PostgreSQL based environement, and when it's all green, you can decide to
push it to production when it best suits you. Almost all last minute
surprise factors have been taken out with this approach.

{{< figure src="/img/keep-calm-and-automate-all-the-things-13.png" >}}
