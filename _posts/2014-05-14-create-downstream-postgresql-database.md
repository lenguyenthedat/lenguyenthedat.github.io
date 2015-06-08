---
layout: post
title: Create a downstream PostgreSQL Database with dblink
date: '2014-05-14T03:26:00.000-07:00'
author: Dat Le
tags: postgresql, dblink, code, data warehouse, aws, tutorial
modified_time: '2014-07-12T23:36:28.620-07:00'
comments: true
blogger_id: tag:blogger.com,1999:blog-3261104696526323937.post-5467635293146884147
blogger_orig_url: http://lenguyenthedat.blogspot.com/2014/05/create-downstream-postgresql-database.html
---

Ran into this request just today, in short, we need to create a downstream Database for our Data Warehouse for better scalability, security, and performance for our internal users.

## Prerequisite:

- An [Amazon Redshift](https://aws.amazon.com/redshift/) cluster as an Upstream Database.
- An [Amazon RDS (PostgreSQL)](https://aws.amazon.com/rds/) instance as a Downstream Database

**Note:** currently - July 2014 - there is a bug that prevents the following query to finish, when you try to do it from an RDS instance type M2, M3 or R3 - the query would run on Redshift for about 15 minutes (you can't even kill it) and ends with a blank error message. Just choose RDS Instance type M1 and it would be just fine.
{: .notice}

## Step-by-step instructions:

- Connect to your instance (Remember to set your instance permission / firewall properly):
{% highlight bash %}
psql -h your.rds.address -p port -U username -d dbname
{% endhighlight %}

- Configuring dblink:
{% highlight sql %}
CREATE EXTENSION dblink;
SELECT dblink_connect('dbname=dbname user=user password=password
                       host=your.redshift.address port=port');
{% endhighlight %}

- Copy a table from Redshift to RDS: 

{% highlight sql %}
SELECT mytable.*
INTO rds_schema_name.rds_table_name
FROM
    dblink('SELECT field_1, field_2 FROM redshift_schema.redshift_table')
AS
    mytable(field_1 text, field_2 text);
Get data from the given table in RDS:
SELECT field_1, field_2
FROM rds_schema_name.rds_table_name;
{% endhighlight %}

## Do it with aws-redshift-to-rds:

In case you need to solve this problem ASAP, here is a little [command-line interface tool](https://github.com/lenguyenthedat/aws-redshift-to-rds) (named: aws-redshift-to-rds) that I've developed while working at Zalora :).