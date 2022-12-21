---
layout: post
title: Managing User Privileges with Amazon Redshift / PostgreSQL
date: '2014-07-18T07:20:00.003-07:00'
comments: true
author: Dat Le
tags: aws, redshift, postgresql, data warehouse, tutorial, zalora
modified_time: '2014-07-18T07:46:52.538-07:00'
thumbnail: http://2.bp.blogspot.com/-D55vGrk18C8/U8kwlHvMoII/AAAAAAAADIc/uc99yN4ldS4/s72-c/Screen+Shot+2014-07-18+at+22.35.41.png
blogger_id: tag:blogger.com,1999:blog-3261104696526323937.post-3941548674700694368
blogger_orig_url: http://lenguyenthedat.blogspot.com/2014/07/managing-user-privileges-with-amazon.html
---

![image](http://2.bp.blogspot.com/-D55vGrk18C8/U8kwlHvMoII/AAAAAAAADIc/uc99yN4ldS4/s1600/Screen+Shot+2014-07-18+at+22.35.41.png)

<br>

Managing user privileges within a system is always a hard problem in general.
In particular, it's even a harder problem for PostgreSQL Database, especially Amazon Redshift (which only supports a subset of PostgreSQL 8.0.2 features).

<br>

Supposed you now have your [Data Warehouse running on Amazon Redshift](http://lenguyenthedat.github.io/building-full-data-warehouse-solution/).

<br>

By default,  you will have all privileges for  Schemas / Tables / Views that you've created or listed as an owner.

<br>

Now, to give read access for a user U1 to a certain table T1 within schema S1, you will then need to execute:

{% highlight sql %}
GRANT USAGE ON SCHEMA S1 TO U1;
GRANT SELECT ON TABLE S1.T1 TO U1;
{% endhighlight %}

<br>

Of course, that's pretty simple and straightforward. However, when you have tens of schemas, thousands of tables to hundreds of users, how would you do it?
A similar problem arises when you then need to drop a user with various access types to a good number of tables in your database (hint: you will need to revoke all those privileges first), or you need to transfer ownership of user X to user Y.

<br>

(Brief) Answer / Hint: you do it programmatically with:

* Query information_schema.schemata; pg_tables; pg_views: for your schemas, tables and views list.
* Store the list into your program.
* Use customized string manipulation functions to create a composed GRANT Query accordingly.
Good luck hacking :).

<br>

PS: In case you need to solve this problem ASAP, here is a little [command-line interface tool](https://github.com/zalora/postgresql-user-manager) (named: postgresql-user-manager) that I've developed while working at Zalora :).
