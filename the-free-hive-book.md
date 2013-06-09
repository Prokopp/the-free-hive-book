![](images/hive-logo.png "Apache Hive (Image © Aaron Newton, http://blog.cloudera.com/blog/2009/04/hive-and-jobtracker-needed-logos/)")

# The _Free_ Hive Book #

by [Christian Prokopp](http://www.semantikoz.com/blog/), [@prokopp](http://www.twitter.com/prokopp), [Google+](https://plus.google.com/106245424622248310303?rel=author)

---

# About This Book #
## License ##
The Little Apache Hive Book is licensed under the [Attribution-NonCommercial 3.0 Unported license](https://creativecommons.org/licenses/by-nc/3.0/).

You are free to share (copy, distribute and transmit the book), you can change the book (extend, fix, shorten, translate, ...).
However, you need to attribute the work to the author, [Christian Prokopp](http://www.semantikoz.com/) [@prokopp](http://www.twitter.com/prokopp).
Lastly, you can not use the work commercially. You can contact me if you like to do so though.

Thank you.

## About The Author ##
[Christian Prokopp](http://uk.linkedin.com/in/prokopp) is a Data Scientist at [Rangespan](http://www.rangespan.com), and writes as [Blogger and Data Journalist](http://www.semantikoz.com/blog) in his spare time. Christian holds a BSc, MCom, PhD and has lived, worked and researched in three continents.

## Why this book? ##
This book was sparked by the need to give some tutorial material to business users at Rangespan. Christian has been working with Hive and Hadoop for the last two years. Giving access to Big Data to business stakeholder through a SQL-like interface has proved extremely valuable and popular. Facebook, a company with one of the largest data sets world-wide for example, heavily relies on Hive and Hadoop for data processing, insight and reporting with thousands of users accessing data spread across thousands of computers.

In that spirit this book provides an example driven introduction to working with Hive. The book helps SQL experienced users to apply their knowledge when working with Hive. At the end of the book are also some more advanced tips for power-users, dev-ops or sysadmins.

## Original and Latest Version ##
The original with the latest updates is available as the [The Free Hive Book](http://www.semantikoz.com/blog/the-free-apache-hive-book/).

<a id="toc"></a>
# Table of Contents #
The book is work in progress and the TOC as well as the actual chapters will evolve.

1. **[Introduction](#introduction) - What is Hive**

2. **[Getting Started](#gettingstarted) - Setup Hive**

3. **[Create, Load, Select, Drop](#createloadselectdrop) - The basics**
	* *Managed Table*
	* *External Table*

4. **[Setting up the example table](#settinguptheexampletable)**

5. **[Query](#query)**
	* *SELECT ... WHERE ...*
	* *SELECT ... ORDER BY ...*
	* *SELECT ... SORT BY ...*
	* *SELECT ... CLUSTER BY ...*
	* *SELECT ... DISTRIBUTE BY ...*
	
6. **Normalize Tables**

7. **[Joining Tables](#joiningtables)**
	* *JOIN*
	* *FULL OUTER JOIN*
	* *LEFT OUTER JOIN*
	* *RIGHT OUTER JOIN*

8. **[Data Storage Formats](#datastorageformats)**
	* *TextFile*
	* *SequenceFile*
	* *RCFile*
	* *ORC*
	* *Parquet*

6. **...**

6. **...**

10. **Tuning Tips**
	* *Controlling File and Split Size*
	* *Data Storage Formats*
	* *Compression*
	* *Partitioning*
	* *Bucketing*
	* *Limit*
	* *Parallel Execution*

<a id="introduction"></a>
#1. Introduction - What is Hive#
([⇪ Table of Contents](#toc))

(If you know Hive and Hadoop you can safely skip the introduction.)

Apache Hive is a data warehouse system build on top of Hadoop to query *Big Data*. Hive originated at [Facebook and was open sourced in August 2008](https://www.facebook.com/note.php?note_id=89508453919). The challenge Facebook had to address is one faced by many companies since then. Eventually data growth in a company challenges the capabilities of deployed RDBMS or NoSQL systems. Reports and analytics start to take minutes, then hours, and eventually overlap with other queries and the whole system grinds to a halt. Another common scenario companies start processing big data with Hadoop discovers the value of making the data accessible beyond the development team capable of writing complex map-reduce jobs.  

##What is Big Data##
The term Big Data is freely used in this context. A colleague defined Big Data jokingly as anything beyond one million rows - Microsoft Excels row limit. The underlying point is that Big Data is a point of view and can be generalised as the point where simple solutions and deployed technology fail.

The subsequent question is if scaling and investing heavily in it is the most economical solution. Commercial large-scale data warehouse solutions are very expensive. Furthermore, some of the data collected today, e.g. poorly structured or highly denormalized data, can be impractical to manage with these systems. The Hadoop ecosystem regularly is utilised to scale data processing in a feasible manner. Hadoop becomes either a replacement or a batch process addition to the existing infrastructure for data analysis, extraction, loading, transformation, reporting, and machine learning.

##Accessing Big Data##
The downside, which Facebook encountered, was that data stored in Hadoop is inaccessible to business users. There are higher-level languages like [Pig](https://pig.apache.org/), [Cascading](http://www.cascading.org/), [Crunch](http://crunch.apache.org) or [Scalding](https://github.com/twitter/scalding). They are, however, geared towards software developers that want to avoid the verbosity of pure Java map-reduce development. Even Pig, a popular data-flow language, still requires users to learn a completely new skill. Facebook realised that most of their users already had a common skill - they knew SQL. Hive was developed to give access to data stored in Hadoop translating SQL-like statements into complex map-reduce jobs reading and processing data on large distributed scale.

##Democratising Big Data##
Hive is a success story. Today, Facebook's largest Hadoop cluster consists of thousands of computers providing a combined storage of 150 Petabytes - roughly 150,000,000 Gigabytes. Hive provides access to literally thousands of employees to the data running together thousands of map-reduce jobs every day using Hive. The additional training for employees knowing SQL to use Hive is minimal. Most statements can be expressed equivalently and full SQL support is coming to Hive very soon.

##What Hive is not##
Hive does not use sophisticated indexes like many RDBMS which are able to answer queries in seconds. Hive queries usually take minutes or hours. However, Hive scales very well and can execute queries across data of the magnitude of Petabytes. The Hadoop ecosystem and Hive are under very active development, however, and speedups are achieved with every new iteration. In 2013 many new developments are due to be introduced, a new Hadoop framework that goes beyond map-reduce and manages resources better, and Hive improvements that will make queries on smaller data faster and interactive.

##What this book will teach##
You will learn how to access data with Hive.

<a id="gettingstarted"></a>
#2. Getting Started - Setup Hive#
([⇪ Table of Contents](#toc))

(You can skip this section if you have access to a Hive CLI (Command Line Interface) or a Hue and Beeswax web interface to Hive.)

Installing Hive for evaluation purposes is best done with a virtual machine provided by one of the popular Hadoop distributions:

* **[Hortonworks](http://hortonworks.com/products/hortonworks-sandbox/)**
* [Cloudera](https://ccp.cloudera.com/display/SUPPORT/Cloudera+QuickStart+VM)
* [MapR](http://www.mapr.com/doc/display/MapR2/Quick+Start+-+Test+Drive+MapR+on+a+Virtual+Machine)

The examples in this book are based on the **Hortonworks Sandbox version 1.2**.

<a id="createloadselectdrop"></a>
#3. Create, Load, Select, Drop - The basics#
([⇪ Table of Contents](#toc))

Hive uses a metastore - a database - to store information about the tables it knows. Tables to Hive often are not much more than storing the information where the data is stored and how it is formatted. We do not have to know much about the metastore itself to use Hive though.

##Managed Table##
Managed tables's data is controlled by Hive. It will create a directory for the data on HDFS and when we drop the table it will delete the data. Later in this chapter we will see that we can manage the data ourselves with an *EXTERNAL* table.

###Creating an empty table###
Let us create a table, which will be a simple list of all names of all countries. Go to the Beeswax query editor and execute the following query:

```sql
CREATE TABLE country_list (name STRING);
```

You can now find the table in the Beeswax table list. Once you selected the table you can view the file location on HDFS which Hive automatically selected and created for you. The table and directory are empty. Note: We can define the location of a new table as we will learn later.

###Describing a table###
We can get all the information we need about a table through a query too. Go back to the query editor and execute:

```sql
DESCRIBE EXTENDED country_list;
```

The result describes the schema of the table and detailed table information. This includes the location of the table and the information that it uses TextInputFormat which is default setting in the Hortonworks Hive setup.

###Loading data into a table###
We want to load data to the table so we need to upload it to HDFS. We know the table has single column of country name, uses a simple text format, and Hive always uses new line characters to as row delimiters. All we need to do to add data is upload one or several files into the directory linked with the table. The files have to be formatted to have one country name on each line.

Go back to the table list and select the `country_list` table. Select `View File Location` and you will be viewing the HDFS directory of where the able stores its data. Upload the `coutry_example.tsv` file into the directory using the file upload function on the top right of the interface.

###Query a table###
Hive is very flexible and checks the table's data location for every query. Adding or removing data on the file system is reflected on the table. After adding the file its content is now automatically retrieved as table data. Try it out. Go back to the Beeswax table list and select the `country_list` again. Click on browse data on the left. You should see four countries which are read from the file. You can also query the table to get the same result. Go tot he query editor and execute:

```sql
SELECT * FROM country_list;
```

###Drop a table###
You can drop the table in the table view of Beeswax through the graphic interface. Go to the query editor and execute the equivalent query to drop the table:

```sql
DROP TABLE country_list;
```

The table and the HDFS directory and file(s) are deleted.


##External Table##
So far the queries and behaviour of Hive has not been very different to SQL systems. Hive's separation of data location and storing the schema in a metastore enables it to create tables pointing to existing data, to read, query, and transform it, and then drop the tables without touching the original data on HDFS.

These unmanaged tables make Hive powerful as a tool that queries outputs from other processes and systems. The terminology used is an extension of the SQL `CREATE TABLE` statement. Let us do the above example again with an external table instead to illustrate the difference.


###Create an external table###
Create the external table by simply adding `EXTERNAL` to the statement:

```sql
CREATE EXTERNAL TABLE country_list (name STRING);
```

Everything will appear to be the same as in the previous example, i.e. the table will look the same in the Beeswax interface and behave the same as the previous one. Only the detailed description reveals a difference:

```sql
DESCRIBE EXTENDED country_list;
```

The table description now mentions `tableType:EXTERNAL_TABLE` which previously was `tableType:MANAGED_TABLE`. The managed table meant that Hive would delete the data on dropping the table - managing it. The external table type means that Hive on dropping a table will remove the schema information only - it disappears from Hive but the data remains on HDFS - Hive considers it external.

Go ahead and try it out. If you repeat our previous example and upload the `country_example.tsv` file to the table's HDFS location the data will appears as table data as before. If you drop the table the data will remain:

```sql
DROP TABLE country_list;
```

Go to the HDFS directory `/apps/hive/warehouse/country_list` to see the data and directory still intact.

###(Re)Creating an external table###
This means that we can create a table with the data on HDFS with a single statement:

```sql
CREATE EXTERNAL TABLE country_list (name STRING);
```

Query the table to see that the data on HDFS has been linked back to the table:

```sql
SELECT * FROM country_list;
```


<a id="settinguptheexampletable"></a>
#4. Setting up the example table#
([⇪ Table of Contents](#toc))

The following examples are based on World Bank data of development indicators of the last four decades. The data is freely available on the [World Bank website](http://databank.worldbank.org/). The data distributed with the book for the examples has been modified for use with Hive.

##Create external table##

```sql
CREATE EXTERNAL TABLE wdi 
(
    country_name STRING,
    country_code STRING,
    indicator_name STRING, 
    indicator_code STRING,
    `1960` FLOAT, `1961` FLOAT, `1962` FLOAT, `1963` FLOAT, `1964` FLOAT,
    `1965` FLOAT, `1966` FLOAT, `1967` FLOAT, `1968` FLOAT, `1969` FLOAT,
    `1970` FLOAT, `1971` FLOAT, `1972` FLOAT, `1973` FLOAT, `1974` FLOAT,
    `1975` FLOAT, `1976` FLOAT, `1977` FLOAT, `1978` FLOAT, `1979` FLOAT,
    `1980` FLOAT, `1981` FLOAT, `1982` FLOAT, `1983` FLOAT, `1984` FLOAT,
    `1985` FLOAT, `1986` FLOAT, `1987` FLOAT, `1988` FLOAT, `1989` FLOAT,
    `1990` FLOAT, `1991` FLOAT, `1992` FLOAT, `1993` FLOAT, `1994` FLOAT,
    `1995` FLOAT, `1996` FLOAT, `1997` FLOAT, `1998` FLOAT, `1999` FLOAT,
    `2000` FLOAT, `2001` FLOAT, `2002` FLOAT, `2003` FLOAT, `2004` FLOAT,
    `2005` FLOAT, `2006` FLOAT, `2007` FLOAT, `2008` FLOAT, `2009` FLOAT,
    `2010` FLOAT, `2011` FLOAT, `2012` FLOAT
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LOCATION '/user/sandbox/wdi';
```

The table is empty since we have not loaded any data yet. Hive created a folder at `/user/sandbox/wdi` on HDFS for us and we can copy data there to appear in the tables.

##Put the data on HDFS##
We first place the data on HDFS for Hive and then create an external table for the data. Upload the `wdi_data.tsv.gz` file to the new HDFS `/user/sandbox/wdi` folder. The file is now located on the distributed HDFS file system and can be read by Hadoop and Hive. The file is Gzip compressed as indicated by the `.gz` postfix. Hive recognises this format and automatically decompresses the file at query time.

<a id="query"></a>
#5. Query#
([⇪ Table of Contents](#toc))

We can check if the schema from the create statement aligns with the data we uploaded by either browsing the data from the Beeswax table interface or querying it:

```sql
SELECT * FROM wdi;
```

The above statement returns all columns and all rows from the table `wdi`.

##SELECT ... WHERE ...##

HiveQL supports `WHERE` constraints on the `SELECT` statement. Let us reduce the selection to a specific indicator. The following query returns all rows for the indicator named `'Trade (% of GDP)'`:

```sql
SELECT * FROM wdi
WHERE indicator_name = 'Trade (% of GDP)';
```

We can further restrict the result to return only the country name and the indicator result of the year 2011:

```sql
SELECT `country_name`, `2011` AS trade_2011 FROM wdi
WHERE indicator_name = 'Trade (% of GDP)';
```

We can also exclude empty `NULL` results for the year 2011:

```sql
SELECT `country_name`, `2011` AS trade_2011 FROM wdi WHERE
  indicator_name = 'Trade (% of GDP)' AND
  `2011` IS NOT NULL;
```

##SELECT ... ORDER BY ...##

What are the countries with the greatest and the least percentage of trade to GDP ratio? The result of the above query can be ordered by column(s). This is similarly to SQL's `ORDER BY ... (ASC|DESC)` statement. Postfixing the order statement with `ASC` or `DESC` will order it in ascending or descending order:

```sql
SELECT `country_name`, `2011` AS trade_2011 FROM wdi WHERE
  indicator_name = 'Trade (% of GDP)' AND
  `2011` IS NOT NULL
  ORDER BY trade_2011 DESC;
```

The downside on ordering globally with `ORDER BY` is that it is implemented using a single reducer. Consequently, ordering a large set of data can take a very long time.

##SELECT ... SORT BY ...##

I cases where you only want to approximate the order or investigate the data the `SORT BY` statement can be used. It sorts the data only in each reducer and not globally. That can be much faster for large data sets.

```sql
SELECT `country_name`, `2011` AS trade_2011 FROM wdi WHERE
  indicator_name = 'Trade (% of GDP)' AND
  `2011` IS NOT NULL
  SORT BY trade_2011 DESC;
```

##SELECT ... CLUSTER BY ...##

Distributed sorting can be very helpful when you have keys to group your sets that you want to order by. For example, we may add another indicator `'Broad money (% of GDP)'` and want the result sorted by indicator to easily split the list in two later.

```sql
SELECT country_name, indicator_name, `2011` AS trade_2011 FROM wdi WHERE
  (indicator_name = 'Trade (% of GDP)' OR
  indicator_name = 'Broad money (% of GDP)') AND
  `2011` IS NOT NULL
  CLUSTER BY indicator_name;
```

The above query will return all 2011 results of all countries for the two indicators where the data is available, i.e. not null. The result will be sorted by indicator and since the input was sorted by country already the result is also sorted by country within each indicator.

It is important to understand the computational benefit. We could have achieved this with a `SORT BY`. However, using `CLUSTER BY` enables Hadoop to distribute the data based on the cluster by key across all computational nodes. It is limited by the cardinality of the key though. If you have only two keys then only two reducers can work in parallel independent of you cluster size.

Examples where `CLUSTER BY` works excellent are where global order is irrelevant. Imagine sorting orders by category and then analyse each category of orders. You may have millions of orders and hundreds of categories. Clustering the sorting would provide a tremendous performance improvement since the sort can potentially be done by hundreds of cluster nodes in parallel.

##SELECT ... DISTRIBUTE BY ...##

`DISTRIBUTE BY` tells Hive by which column to organise the data when it is sent to the reducers. We could instead of using `CLUSTER BY` in the previous example use `DISTRIBUTE BY` to ensure every reducer gets a complete set of indicators.

```sql
SELECT country_name, indicator_name, `2011` AS trade_2011 FROM wdi WHERE
  (indicator_name = 'Trade (% of GDP)' OR
  indicator_name = 'Broad money (% of GDP)') AND
  `2011` IS NOT NULL
  DISTRIBUTE BY indicator_name;
```

![DISTRIBUTE BY indicator_name](images/distribute_by_distribute_by_indicator_name.png "SELECT country_name, indicator_name, `2011` AS trade_2011 FROM wdi WHERE
  (indicator_name = 'Trade (% of GDP)' OR
  indicator_name = 'Broad money (% of GDP)') AND
  `2011` IS NOT NULL
  DISTRIBUTE BY indicator_name;")

If you ran the example on the Hortonworks VM or any other setup with one reducer your query result will look like the rows are not organised by indicator names. The difference is that `DISTRIBUTE BY` does not sort the result. It ensures that all rows with the same indicator are sent to the same reducer but it does not sort them as `CLUSTER BY`. Consequently, all rows were sent to the one reducer you have and the output is mixed. `CLUSTER BY` is in fact only syntactic sugar for a combination of `DISTRIBUTE BY` and `SORT BY`. The latter adding the local sorting on each reducer.

```sql
SELECT country_name, indicator_name, `2011` AS trade_2011 FROM wdi WHERE
  (indicator_name = 'Trade (% of GDP)' OR
  indicator_name = 'Broad money (% of GDP)') AND
  `2011` IS NOT NULL
  DISTRIBUTE BY indicator_name
  SORT BY indicator_name;
```

![DISTRIBUTE & SORT BY indicator_name](images/distribute_sort_by_indicator_name.png "SELECT country_name, indicator_name, `2011` AS trade_2011 FROM wdi WHERE
  (indicator_name = 'Trade (% of GDP)' OR
  indicator_name = 'Broad money (% of GDP)') AND
  `2011` IS NOT NULL
  DISTRIBUTE BY indicator_name
  SORT BY indicator_name;")

The result should be equivalent with the cluster example and in each reducer the rows were sorted.

[Here be dragons]

<a id="joiningtables"></a>
#7. Joining Tables#
([⇪ Table of Contents](#toc))

##Setup Data##
Create two tables with list of OECD and NATO countries for the join examples.

```sql
CREATE TABLE oecd_countries (name STRING, year STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';
```

```sql
CREATE TABLE nato_countries (name STRING);
```
Finally go to `/apps/hive/warehouse/nato_countries` and `/apps/hive/warehouse/oecd_countries` in the Hue filebrowser (or use the HDFS put command) and upload `nato_countries.tsv` and `oecd_countries.tsv` in the corresponding directories.


##JOIN##

Joins are very common operations to combine related tables and *join* them on a shared value.

NoSQL and big data architectures tend to diverge from traditional table designs and normalisations to reduce JOINs. Until recently duplication of columns in tables was seen as wasteful and hard to change. The `wdi` table has a country name and country code column. Traditionally tables would only contain an identifier/code and we would store all country relevant information including the full name in a separate country table. Since storage has become cheap and plentiful we can observe a tendency to duplicate commonly used information in tables like country name to reduce the need for JOINs. This simplifies daily operations, analytics, and saves computation on JOINs on the expense of storage.

However, there are plenty of examples where JOINs are needed. The most common example is the inner JOIN. In our OECD and NATO toy example we can join the two list to see which countries are members of both organisations.

```sql
SELECT o.name
FROM oecd_countries o
JOIN nato_countries n
ON n.name=o.name;
```

We declared the aliases **o** and **n** after mentioning the tables in the query to simplify the query `...ON n.name=o.name;`.


![(INNER) JOIN](images/join_inner.png "SELECT o.name
FROM oecd_countries o
JOIN nato_countries n
ON n.name=o.name;")

The results above show only rows that satisfy the JOIN, i.e. have a matching country name in both `nato_countries` and `oecd_countries`.


##FULL OUTER JOIN##

An outer join ensures that the result contains a row for each input table. It does not matter if the left *or* the right side of the join has null values for a right *or* left part of the join.

```sql
SELECT o.name AS oecd_country, n.name nato_country
FROM oecd_countries o
FULL OUTER JOIN nato_countries n
ON n.name=o.name;
```

![FULL OUTER JOIN](images/join_full_outer.png "SELECT o.name AS oecd_country, n.name nato_country
FROM oecd_countries o
FULL OUTER JOIN nato_countries n
ON n.name=o.name;")

##LEFT OUTER JOIN##

##RIGHT OUTER JOIN##

##Optimising Joins##
###Map JOIN###
`SET hive.auto.convert.join=true;`
###Avoid NULL values in JOIN###
`... FROM y JOIN x ON y.a=x.a AND y.a IS NOT NULL ...`

<a id="datastorageformats"></a>
#8. Data Storage Formats#
([⇪ Table of Contents](#toc))

##TextFile##
```sql
CREATE TABLE country (
	name STRING,
	states ARRAY<STRING>,
	cities_and_size MAP<STRING, INT>,
	parties STRUCT<name STRING, votes FLOAT, members INT>
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\001'
COLLECTION ITEMS TERMINATED BY '\002'
MAP KEYS TERMINATED BY '\003'
LINES TERMINATED BY '\n'
STORED AS TextFile;
```
The `TextFile` format is the default for Hive. It uses four delimiters to split an output or input file into rows, columns and complex data types. It can be explicitly expressed with the `STORED AS TextFile` expression.

The definition of the delimiters follows the table's schema definition and has to have `ROW FORMAT DELIMITED` prepended. Misleadingly `LINES TERMINATED BY '\n'` is a valid statement. It describes the default splitting of rows by newline character. Unfortunately, this delimiter can not be set to anything else but newline.

Most commonly the field delimiter is changed to comma (`FIELDS TERMINATED BY ','`) or tab (`FIELDS TERMINATED BY '\t'`). The default is 0x01 or ^A also known as Ctrl-A. The default for collection items is 0x02 and 0x03 for map keys.

It is important understand that Hive does **not** support escaped characters. For example, a sheet saved as a CSV file from Excel may be formated with double quotation to escape commas in fields:

`"One, two, or three.", 1.01, 2012"`

Defining a TEXTFILE input format with comma as a delimiter would not return the expected columns since Hive would parse the row as the following columns:

`"One`, `two`, `or three."`, `1.01`, and `2012`

###Delimiters and Gotchas###

###Compression###

##SequenceFile##
```sql
CREATE TABLE country (name STRING, population INT)
STORED AS SequenceFile;
```
The `SequenceFile` format is the Hadoop default and stores data in a binary key-value pairs. It is optimised for fast and block size IO. Most tools in the Hadoop ecosystem can read SequenceFile format.

Hive stored its row data in the value part of the pairs. This format is more efficient than plain text and it can be compresses with the available formats in Hadoop either by value (row) or block (data chunk). The latter is usually more efficient since a compression is more likely to find reducible patterns in a large data block than in a row.

The disadvantage of this format is that Hive has to read and parse every row for every query (presuming no partitioning) to execute the query conditions against it. This can lead to unnecessary read operations if only some rows or some columns are relevant to the query.

##RCFile##
```sql
CREATE TABLE country (name STRING, population INT)
STORED AS RCFile;
```

The Row Columnar File (RCFile) format combines row and column oriented storage principles. Each file stores one or more row groups, which is a number of rows. The row groups themselves are stored in a columnar structure.

For example, a file may store rows 1-1,000 in the first group and row 1,001 to 2,000 in the next and both groups in one RCFile. The row group itself stores all the columns together. The first group then would save the first column of row 1 to 1,000 and then next column and so forth.

The benefit of grouping columns is a more efficient compression since similar data is near to each other. More importantly query conditions can be pushed down to read only relevant parts of a table. This is especially helpful with wide tables and queries that only apply to a few columns. In these cases Hive can skip large parts of the data to save IO and computing time.
##ORC##

##Parquet##


<a id="partitioning"></a>
#9. Partitioning#
([⇪ Table of Contents](#toc))

date, region, country, category, boolean ... IS NULL.


[Here be dragons]
