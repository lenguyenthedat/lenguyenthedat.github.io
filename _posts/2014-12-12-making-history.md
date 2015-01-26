---
layout: post
title: Storing historical data within your relational database
date: '2014-12-12T15:06:00.001-08:00'
comments: true
author: Dat Le
tags:
modified_time: '2014-12-12T10:16:15.429-08:00'
---

![image](http://i.imgur.com/6uJIfTf.png)

**Problem description**: We have a table named `products` with 2 columns: `product_id` and `stock`. We wants to record stock movement for all products, but storing a snapshot of `products` table everyday is just inefficient in term of space and time.
{: .notice}

<br>

As a starter, we are going to create two `products` tables for `yesterday` and `today`:

**Note:** We are going to use PostgreSQL syntax for this blogspot, since PostgreSQL is my favorite relational database. You should be able to do it with MySQL or any other relational database.
{: .notice}

{% highlight sql %}
CREATE TABLE products_yesterday (product_id bigint, stock int); -- 2014-01-15
INSERT INTO products_yesterday VALUES
    (1,25), (2,35), (4,20), (5,1), (6,55), (7,10);
{% endhighlight %}

{% highlight sql %}
SELECT * FROM products_yesterday ORDER BY product_id;

 product_id | stock 
------------+-------
          1 |    25
          2 |    35
          4 |    20
          5 |     1
          6 |    55
          7 |    10
(6 rows)
{% endhighlight %}

{% highlight sql %}
CREATE TABLE products_today (product_id bigint, stock int); -- 2014-01-16
INSERT INTO products_today VALUES
    (1,20), (3,10), (4,0), (5,15), (6,55), (7,10), (8,50), (9,20);
{% endhighlight %}

{% highlight sql %}
SELECT * FROM products_today ORDER BY product_id;

 product_id | stock 
------------+-------
          1 |    20
          3 |    10
          4 |     0
          5 |    15
          6 |    55
          7 |    10
          8 |    50
          9 |    20
(8 rows)
{% endhighlight %}

**Making history:**

**Note:** We're going to follow [SCD (Slowly Changing Dimension) Type 2](http://en.wikipedia.org/wiki/Slowly_changing_dimension#Type_2) method. This is, in a way, very similar to the how Apple's Time Machine works.
{: .notice}

- *Step 1*: create history table from `products_yesterday` (with `start_date` as yesterday's date and `end_date` as `infinity`:

{% highlight sql %}
SELECT product_id, stock INTO products_history FROM products_yesterday;
ALTER TABLE products_history ADD COLUMN start_date TIMESTAMP;
ALTER TABLE products_history ADD COLUMN end_date   TIMESTAMP;
UPDATE products_history SET start_date = '2014-01-15 00:13:37' WHERE start_date is NULL;
UPDATE products_history SET end_date   = 'infinity' WHERE end_date is NULL;
{% endhighlight %}

**Note:** after this, `products_history` should contain the same amount of data as `products_yesterday`, if we are ignoring `start_date` and `end_date` columns.
{: .notice}

{% highlight sql %}
SELECT * FROM products_history ORDER BY product_id;

 product_id | stock |     start_date      |           end_date           
------------+-------+---------------------+------------------------------
          1 |    25 | 2014-01-15 00:13:37 | infinity <-- Current data
          2 |    35 | 2014-01-15 00:13:37 | infinity <-- Current data
          4 |    20 | 2014-01-15 00:13:37 | infinity <-- Current data
          5 |     1 | 2014-01-15 00:13:37 | infinity <-- Current data
          6 |    55 | 2014-01-15 00:13:37 | infinity <-- Current data
          7 |    10 | 2014-01-15 00:13:37 | infinity <-- Current data
(6 rows)
{% endhighlight %}

- *Step 2*: update history table with the latest data from `products_today` table:

{% highlight sql %}
-- For those that has been modified / removed (history `excepts` today)
-- => Invalidate them by setting `end_date` to `today`
CREATE TEMPORARY TABLE history_excepts_today AS (
    (SELECT product_id, stock FROM products_history WHERE end_date = 'infinity')
        EXCEPT
    (SELECT product_id, stock FROM products_today)
);

UPDATE products_history SET end_date = '2014-01-16 00:13:37'
FROM history_excepts_today
WHERE products_history.end_date = 'infinity'
AND products_history.product_id = history_excepts_today.product_id
AND products_history.stock = history_excepts_today.stock;
{% endhighlight %}

{% highlight sql %}
SELECT * FROM products_history ORDER BY product_id;

 product_id | stock |     start_date      |           end_date           
------------+-------+---------------------+------------------------------
          1 |    25 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          2 |    35 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          4 |    20 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          5 |     1 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          6 |    55 | 2014-01-15 00:13:37 | infinity <-- Current data
          7 |    10 | 2014-01-15 00:13:37 | infinity <-- Current data
(6 rows)
{% endhighlight %}

{% highlight sql %}
-- For those that has been modified / added (today `excepts` history)
-- => Insert into history with `start_date` as `today`
CREATE TEMPORARY TABLE today_excepts_history AS (
    (SELECT product_id, stock FROM products_today)
        EXCEPT
    (SELECT product_id, stock FROM products_history WHERE end_date = 'infinity')
);

INSERT INTO products_history
SELECT *, '2014-01-16 00:13:37' start_date, 'infinity' end_date
FROM "today_excepts_history";
{% endhighlight %}

{% highlight sql %}
SELECT * FROM products_history ORDER BY product_id;

 product_id | stock |     start_date      |           end_date           
------------+-------+---------------------+------------------------------
          1 |    25 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          1 |    20 | 2014-01-16 00:13:37 | infinity <-- Current data
          2 |    35 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          3 |    10 | 2014-01-16 00:13:37 | infinity <-- Current data
          4 |    20 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          4 |     0 | 2014-01-16 00:13:37 | infinity <-- Current data
          5 |     1 | 2014-01-15 00:13:37 | 2014-01-16 00:13:37 <-- Invalidated
          5 |    15 | 2014-01-16 00:13:37 | infinity <-- Current data
          6 |    55 | 2014-01-15 00:13:37 | infinity <-- Current data
          7 |    10 | 2014-01-15 00:13:37 | infinity <-- Current data
          8 |    50 | 2014-01-16 00:13:37 | infinity <-- Current data
          9 |    20 | 2014-01-16 00:13:37 | infinity <-- Current data
(12 rows)
{% endhighlight %}

**Using historical tables:**

- To get data at any point in time, for example: `2014-01-15 01:13:37`:

{% highlight sql %}
SELECT product_id, stock FROM products_history
WHERE '2014-01-15 01:13:37' between start_date AND end_date ORDER BY product_id;

 product_id | stock 
------------+-------
          1 |    25
          2 |    35
          4 |    20
          5 |     1
          6 |    55
          7 |    10
(6 rows)
{% endhighlight %}

**Note:** This should give you the same result as `product_yesterday`.
{: .notice}

- To get the latest data:

{% highlight sql %}
SELECT product_id, stock FROM products_history 
WHERE end_date = 'infinity';

 product_id | stock 
------------+-------
          1 |    20
          3 |    10
          4 |     0
          5 |    15
          6 |    55
          7 |    10
          8 |    50
          9 |    20
(8 rows)
{% endhighlight %}

**Note:** This should give you the same result as `product_today`.
{: .notice}