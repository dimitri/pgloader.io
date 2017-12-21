---
author: "Dimitri Fontaine"
date: 2017-10-30
title: pgloader, Your Migration Companion
best: false
---

# PostgreSQL Conference Europe 2017

[PGConf.EU](https://2017.pgconf.eu/) is a unique chance for European
PostgreSQL users and developers to catch up, learn, build relationships, get
to know each other and consolidate a real network of professionals that use
and work with PostgreSQL.

# pgloader, your migration companion 

Migrating data from another RDBMS to PostgreSQL should be really easy. After
all it’s all relations and tables and the same data types everywhere or
abouts, with text, dates and numbers. Well it’s actually quite messy and
complex to migrate the data properly from one system to another. pgloader
solves this problem and turns it into a one-liner: in this talk you’ll learn
how to benefit from that, and the classic pitfalls to avoid.

# The Slides

If you were not present at [PGConf.EU](https://2017.pgconf.eu/) in Warsaw,
you might still want to read the following slide deck:

<center>
  <a href="/img/PGCONF_EU_2017_pgloader.pdf">
    <img src="/img/PGCONF_EU_2017_pgloader.png" style="width: 317px; height:238px">
  </a>  
</center>

As far as I know, we don't have a video recording of this talk, sorry.

# An Older Video about using Common Lisp

This video is a Lightning Talk I gave at the [European Lisp
Symposium](https://www.european-lisp-symposium.org/2014/index.html), where I
introduced why pgloader is now written in Common Lisp rather than in Python
like before.

Spoiler alert: that's because Common Lisp allows for a much faster program
(both runtime and time spent writing and fixing the code), and a host of new
features that I could not have developed in Python in any comparable amount
of time…

<center>
<video id="pg-lt" width="100%" controls data-title="Lightning Talks">
  <source src="https://medias.ircam.fr/stream/ext/video/files/2014/05/13/ELSAA_6_mai2.mov.webm#t=57,357" type="video/webm" />
  <source src="https://medias.ircam.fr/stream/ext/video/files/2014/05/13/ELSAA_6_mai2.mov.mp4#t=57,357" type="video/mp4" />
  <source src="https://medias.ircam.fr/stream/ext/video/files/2014/05/13/ELSAA_6_mai2.mov.ogg#t=57,357" type="video/ogg" />
</video>
</center>
