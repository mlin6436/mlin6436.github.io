---
layout: post
title: "Setup PostgreSQL on PC"
tags: ["postgresql", "windows", "pc", "orm", "pgadmin", "admin", "restore", "database", "devart"]
---

<div class="message">
After few years working on MSSQL, I almost forgot there are still a whole world of databases out there to be explored, and a lot of which are free and lightweight, such as SQLite, PostgreSQL.
</div>

Long story short, I cracked on setting up a PostgreSQL as it is work-related, and I probably should have done it on Linux. But the web portal is .NET based, so here we are.

# Install PostgreSQL and pgAdmin

You can find a copy of PostgreSQl and pgAdmin right [here](http://www.postgresql.org/download/windows/).

Open the pgAdmin, and the fun begins. Go `File` -> `Add Server` to create a new PostgreSQL server, and type in the following settings:

{% include image.html url="/media/2013-09-28-setup-postgresql-on-pc/postgresql-01.png" width="100%" description="pgAmin settings" %}

After clicking `OK`, you should be able to see the new instance being created.

{% include image.html url="/media/2013-09-28-setup-postgresql-on-pc/postgresql-02.png" width="100%" description="New PostgreSQL server instance" %}

# Restore a Database

Now you can choose to create a new database or restore from backup. Since I was buggered by restoring, I am going to show you how I dealt with it.

The backup file I hold is in plain text .backup file (bad I know, but because there is no confidential information, so I did). It stops me from running direct restore, and prompted me this message `input file appears to be a text format dump. Please use psql`.

This means I have to use `psql` tool to restore the database manually:

{% highlight bash %}
$ cat <file>.backup | psql <db>
{% endhighlight %}

# ORM

Setting up a PostgreSQL seems to be a nice and painless experience, but how do I consume it?

One of the preferable way is to use ORM, and with the help from tools like [Devart](http://www.devart.com/), it really is easier done than said. Devart is a .NET library to access data using database-first or model-first, similar to Entity Framework, but without the support for code-first as far as I notice. The only downside is Devart is not free.

By installing [dotConnect for PostgreSQL](http://www.devart.com/dotconnect/postgresql/download.html) plugin, you will get more options when creating data access models in Visual Studio.

{% include image.html url="/media/2013-09-28-setup-postgresql-on-pc/postgresql-03.png" width="100%" description="dotConnect for PostgreSQL plugin" %}

Choose `database first` to start configuring.

The problem I came across is a network setting issue which suggests me I am not using proper SSL.

{% include image.html url="/media/2013-09-28-setup-postgresql-on-pc/postgresql-03.png" width="100%" description="SSL issue for PostgreSQL connection" %}

Since we are not communicating with any public network, I find the file `C:\Program Files\PostgreSQL\<version>\data\pg_hba.conf`, and hack through it by adding `host all all 0.0.0.0/0 md5` to the config. Then everyone's happy!

After this, you can very much keep clicking next until finish if you don't have any specific requirements. And you will find yourself in a place as if you are using LinqToSQL.

# Conclusion

PostgreSQL is probably not as powerful as MSSQL, but you get most of day to day functions if you are not hardcore DBA. It can be hosted on different platforms, and most importantly it is free.

Since I only started using PostgreSQL, I find its UI is somehow alien, and could be a bit clunky to use sometimes. The syntax has its own vein of blood when it comes to write functions.

ORM is definitely the best way to access data, yet Devart does not give you everything for free. Perhaps this will not be a problem when community sorts out code first. Then we can truly enjoy the convenience and freedom of using PostgreSQL.
