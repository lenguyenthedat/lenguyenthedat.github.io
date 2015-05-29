---
layout: post
title: 'Redshift Error: exceeded the maximum number of cursors allowed to be open concurrently.'
date: '2015-04-10T17:45:00'
comments: true
author: Dat Le
tags: bug, aws, redshift, data warehouse, tutorial
modified_time: 2015-04-10T17:45:00'
---

Encountered this bug trying to integrate [Tableau](http://www.tableau.com/) with [Amazon Redshift](http://aws.amazon.com/redshift/) the other day, figured I should note it down somewhere...

<br>

![image](http://i.imgur.com/dc2vsN3.png)

###Root cause:

Basically stated [here](http://docs.aws.amazon.com/redshift/latest/dg/declare.html):

>If your client application uses an ODBC connection and your query creates a result set that is too large to fit in memory, you can stream the result set to your client application by using a cursor. When you use a cursor, the entire result set is materialized on the leader node, and then your client can fetch the results incrementally.

So, the bigger your `Result Set Size per Cursor` is, the more time you save retrieving the query result. However, the smaller your cluster is, the lower `Result Set Size`, lower number of concurrent query it can support. It's basically a trade-off between speed and convenience. Different Redshift cluster type has different constraints on `Result Set`, as described [here](http://docs.aws.amazon.com/redshift/latest/dg/declare.html#declare-constraints):

>| Node Type | Max RS per Cluster | Max RS per Cursor | Concurrent Cursors |
|----------|-------------|------|------|
| DW1 XL single node | 64000 | 32000 | 2 |
| DW1 XL multiple nodes | 1800000 | 450000 | 4 |
| DW1 8XL multiple nodes | 14400000 | 960000 | 15 |
| DW2 Large single node | 16000 | 16000 | 1 |
| DW2 Large multiple nodes | 384000 | 192000 | 2 |
| DW2 8XL multiple nodes | 3000000 | 750000 | 4 |
    
###Solution:

- Go to your [Redshift cluster setting](https://console.aws.amazon.com/redshift/home) and edit your `Parameter Group` (You will need to create a new one if you haven't done so before):

<br>

![image](http://i.imgur.com/m18lT5W.png)

<br>

- Update `max_cursor_result_set_size` to a smaller number depending on your usage and cluster size:

<br>

![image](http://i.imgur.com/xMgHHuL.png)

<br>

- Modify your cluster - change to newly created `Parameter Group` and reboot:

<br>

![image](http://i.imgur.com/NcUgojI.png)
