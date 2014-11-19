---
title: An Introduction to SQL Databases for Data Analysis
---
#An Introduction to SQL Databases for Data Analysis - Part I: SQLite

This two part module provides a basic introduction to the SQLite database and the SQL database query language. This first part introduces SQLite and illustrates how to start working with it. The second part demonstrates how we can start analysing a real dataset using SQL.

## Module Objectives
* Understand basic SQL
* Understand importing and exporting of Data
* Understand how you can use SQL to do analysis

## Prerequisites
* [*SQLite*](http://www.sqlite.org/) - a simple, self-contained, serverless relational database.
* The [*enhetsreistered* SQLite database](http://datahub.io/dataset/enhetsregisteret-norway/resource/a01af5e0-1c70-42ed-afc4-b733652972e2) - the Norwegian company register.

## Introduction
Spreadsheets are great for data analysis but when it comes to larger amounts of data or more complex data structures - databases really outperform them. The most common type of databases used are known as *relational databases*, with database management systems such as MySQL, PostgreSQL, and MariaDB tending to be the most popular examples. Data is stored in separate *tables*, with rows representing particular items and columns the various properties of each item.

Data may be stored across several different tables with common identifiers or *index keys* linking the data in one table with the data in another. So for example, one table might contain the registered name and address of a company, with a company number used as a unique identifier. Another table might contain information about different directors associated with the company, with each row associating a particular director number with a particular company number. A third table might associate company numbers and contracts, and so on.

The most common way of getting information into, and out of, a relational database is to use a language known as SQL - *Structured Query Language*. Whilst each relational database tends to use a slightly different dialiect of SQL from the others, don’t be afraid - the basics are generally the same.

## The Basics

As we have already mentioned, many database management systems support SQL. Whilst we could install an industry strength system to learn SQL with, there is also a much more convenient - and cross-platform - route we can get started with: *SQLite*.

*SQLite* is a self-contained database system that doesn't require any sort of server support, which means it's easy to get up an running. To work through this activity, you will need to install *SQLite* on your computer.

[Download and install SQLite](http://www.sqlite.org/download.html): *find the appropriate* ***Precompiled Binaries*** *link for your platform and download and unzip the corresponding file*.

## Starting SQLite
The L in SQL stands for language - that means you have to speak to your computer - on a command line. Don’t be afraid: the basics of the language are not too hard to learn (and for more complex things there is always internet search). 

On a Mac or on Windows, double click on the application file to start it. SQLite starts up in a command line. You should now see some information about the version of SQLite you’re using and a prompt (saying `sqlite>`). 

```
SQLite version 3.8.7.1 2014-10-29 13:59:56
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite>
```

If you're concerned about being stuck in terminal hell here, DON'T PANIC: you can always exit SQLite by typing `.quit`.

The first thing we want to look at is some built in functions of SQLite. Type `.help` followed by enter to see some of the commands you have in there - this is useful for reference. Note: these "dotted" commands are not part of the SQL language.

### Creating your first table
Data in relational databases is stored in tables. To use a spreadsheet as an analogy: a database is a workbook, a table is a single sheet. As in well designed spreadsheets, data is organized in rows and columns. Each row represents a specific data point with different variable or attribute values associated with each column. Unlike spreadsheets, databases are a lot more stringent when it comes to what sort of thing you put into a column: for example, is it an integer number, a real number (with a decimal point), a date, a few tens of characters, or maybe hundreds of characters, and so on.

Before we store our data, we need to explicitly define what is to be stored in a table and what sort of thing we want to have in each column. Let’s do this first with a small example dataset.

| Name  | Mood  | Age  |
|---|---|---|
| Michael | Grumpy | 33 |
| Anders | Happy | 35 |
| Zara  | Happy | 28 |


First, we need to define the columns - we have *Name* (which is text), *Mood* (also text) and *Age* (a number without decimals). We need to tell the computer to make a table with those column names and value types.

As with natural language, we use words to tell the computer what we want: 

`CREATE TABLE people (name char(255), mood char(255), age int);`

That wasn't so hard, was it?

Note that our text has become a `char` type. As names are reasonably short, we estimate the maximum length for a name at 255 characters and write `char(255)` to save space in the database for names that are up to 255 characters long. We define `mood` in a similar way. As `age` is a whole number, we define it as an *integer* type, or `int`. The semi-colon `;` at the end of the statement tells SQLite that we have finished that statement.

You may also have noticed that there are certain words written in all upper case characters. This is not required but standard convention in SQL: it makes it easier to read for humans!

We have now created our first table. We can find it using the SQLite command `.tables`. and look at its definition using `.schema`. This  is a very easy and small table; for larger tables, and for situations where data is stored across multiple tables, we will use one or more columns to uniquely identify each row. This use of unique key values speeds up the retrieval of particular items, which is particularly useful when working with large datasets.

Now let’s add some data to our `people` table - we will tell the database to insert a record for Michael, who is grumpy and 33 years old. In SQL, we write this as the command:

`INSERT INTO people VALUES ('Michael','Grumpy',33);`

The order is of items needs to be the same as the order of the columns in the original table. We also need to make sure that the data value is the right sort of thing (a string of characters for the name and mood, an integere for the age).

Let’s do the same for Anders and Zara, who both have a happier disposition:

```
INSERT INTO people VALUES ('Anders','Happy',35);
INSERT INTO people VALUES ('Zara','Happy',28);
```

Fantastic! We’ve entered some information into the table! Note: You can omit the column names only if you’re entering all values and in the order of the columns. Otherwise you have to specify the column. E.g. if we don’t want to enter a mood, we can use: 

`INSERT INTO people (name, age) VALUES ('Tom', 33);`

So having got some data into our table, how can we get it out again?

## Filtering and Sorting SQL style
Let’s now look at our data. To see the contents of our table we can tell the database: *Show me everything in the `people` table*. In SQL:

`SELECT * FROM people;`

Note the `*` - it is a “wildcard” character. This means as much as select _all_ the columns.

The database will respond with:

```
Michael|Grumpy|33
Anders|Happy|35
Zara|Happy|28
Tom||33
```

Fantastic! We can now retrieve our data. But databases would be pretty useless if all we could do was get a list of everything we had stored in the database in the order in whcih we had stored it.

In a giant database, you might want to get all the records that correspond to a particular entry (name = ‘John’, age = 51) or a particular subset of rows. SQL queries allow us to write very powerful filters. Let’s get a list back of just the *happy* people. We’ll tell the database show me everything in the people table if the mood is happy. In SQL:

`SELECT * FROM people WHERE mood=’Happy’;`

Databases can do this very very quickly even if the dataset you work with is very large and the rows you want spread throughout the database. Note the `WHERE` clause - this defines our filters (here, give me all the columns (`*`) for all the rows where the mood is happy). You can combine multiple conditions with AND, OR and NOT.

Another thing we might want to do is sorting (and figuring out the range of values within a column).

Let’s first sort the dataset by age - youngest first, which is to say, ordered by age *ASC*ending. We can also choose to return just the name and age columns in the results:

`SELECT name, age FROM people ORDER BY age ASC;`

You can specify which columns you want as part of the results, and the order in which you want the columns to be arranged, if you list them with comma separators immediately following the SELECT keyword.

Wasn’t that easy? Of course you can combine filters and sorting.  `ORDER BY` serves as another kind of filter, where `ASC` stands for *ASC*ending and `DESC` for descending. Sorting can also be across multiple columns, as in the following example:

`SELECT name, age FROM people ORDER BY age DESC, name ASC;`

## Maxima and Minima

Let’s figure out the maximum and minimum ages of people in out table.

We'll start with the *MAX*imum age of the people we know about:

`SELECT MAX(age) FROM people;`

Note this will only return the maximum age - not the entry with the maximum age.

Can you guess how to find the *MIN*imum age?! 

## Nested Queries
A very powerful feature of SQL is that allows us to use nested queries (queries within queries). For example, if we want to find out which people have the maximum age we can use:

`SELECT * FROM people WHERE age=(SELECT MAX(age) FROM people);`

And see it’s Anders!

## Exporting and Importing Data

As well as querying data contained within a database, we may also need to be able to get data into and out of the database in bulk.

### Exporting Data
Often you don’t want to have the data you just analyzed live in the SQL database only - you might want to export the results of a particular query so that you can work with it elsewhere. SQLite supports data export in a variety of formats. To export a dataset, we need to say how we want to export it, and only then can we actually export it.

Using the command `.mode` allows you to set the output mode - e.g. `.mode csv` sets the output format as CSV. If you set the `.header on`, you will also get a header row.

Now set `.output` to the file you want to write: `.output myexport.csv`, for example.  Now the output of any query you run will be written to the export file. Setting `.output stdout` reverts the output back to printing on your screen. 

If you export data like this, we recommend that you refine your query until it gives the sort of anwser you expect in the terminal before you set the output mode to write to a file. While testing your query, you can use the `LIMIT N`setting to limit the number of rows that are displayed in your answer. For example:

`SELECT * FROM people ORDER BY age DESC LIMIT 1;`

will return just a single row. Furthermore, it will have ordered the table by descending age before providing the result - which means that the row displayed will actually correspond to one of the people in the table with the oldest age.

### Importing Data
We have already seen how to get data into a predefined table using the `INSERT` command. Importing data works very similar way to the export route. If your data is in a .csv file, find out what columns you have and what data is in there, then create your table (for example, `mytable`) accordingly. If you then set `.mode csv` as described above, you can import data into your predefined table using the command `.import myfile.csv mytable`.

Note that any header row is likely to be imported as data - so either edit it out of the CSV data file before import, or learn how to `DELETE` rows from the database!

## Summary

In the first part of this module, you have learned how to create simple database table using SQLite, how to populate it with individual data elements as well as data contained in a CSV file, and how to export data from it. You have also learned how to use SQL to run some simple queries on the data using the SELECT statement, allowing you to retrieve specifically matched items using the WHERE clause, how to order results using ORDER BY, and how to limit the number of results returned usinf the LIMIT statement. 

In the second part of this module, you will see how to use SQL and SQLite for a rather more elaborate data investigation.

# An Introduction to SQL Databases for Data Analysis - Part II: Investigating a Real Dataset

This two part module provides a basic introduction to the SQLite database and the SQL database query language. The first part introduced SQLite and illustrates how to start working with it. This second part demonstrates how we can start analysing a real dataset using SQL.

## Module Objectives
* Understand basic SQL
* Understand importing and exporting of Data
* Understand how you can use SQL to do analysis

## Prerequisites
* [*SQLite*](http://www.sqlite.org/) - a simple, self-contained, serverless relational database.
* The [*enhetsreistered* SQLite database](http://datahub.io/dataset/enhetsregisteret-norway/resource/a01af5e0-1c70-42ed-afc4-b733652972e2) - the Norwegian company register.


Let’s now go and do some data analysis using SQL. We’ll ask a series of questions and try to answer it using SQL. As our questions get more refined, the SQL queries we will build will also get more and more complex.

## Opening the Database
In your SQLite prompt type `.open PATH/enhetsregisteret.sqlite` - this will open the database. (*PATH* is the route to the directory the .sqlite file is saved in. If the file is in the same directory as the SQLite application, you do not need to specify the path, just the sqlite file name.)


If you look which tables there are - there is just one: `companies`. Look at the schema to see what fields are there.

###How many companies are we dealing with?
One of the first things we often want to do with a dataset that is new to us is to find out how large it is. In this case, we want to find out how many companies are listed in the database. In SQL, we COUNT the entries in a specific column. Many tables use an ID column to contain a unique identifier for a each row. Such columns are usually good ones to count becuase they should always contain a value. (If a row has an empty column value, it may not get counted.) 

`SELECT COUNT(id) FROM companies;`

This will tell you - perhaps after a second or two! -  that we have roughly one million companies listed in the database.

###What types of companies do we have?
In the data you’ll see a column called `type` - this is the type of the company that is registered. Let’s see which different types we have using the DISTINCT filter to find unique values contained within a particular column:

`SELECT DISTINCT(type) FROM companies;`

Can you find out how many types there are (Hint: You can combine expressions such as COUNT and DISTINCT) ? And can you find out how many different `cities` are ideintifed?

###Which are the most common company types?
The next thing we might ask ourselves is which company types are the most common. For this, we will need to count the number of companies grouped into each category of company type. SQL does this with the GROUP BY statement:

```
SELECT type, COUNT(id) 
	FROM companies 
	GROUP BY type;
```

Let's just pick that command apart. We are returning two columns in out results data: each unique type, and a count of the number of ids within each type. The GROUP BY operator splits the companies up into groups, where each group contains rows that are of the same company type. The COUNT command then counts the number of rows in the group, and associates the count with the particular company type.

###Can we sort the results?
You have already seen how it's possible to sort results by value. We can also sort on our result counts. If we limit the number of results returned, we can list just the top 5:

```
SELECT type, COUNT(id) AS typecount 
		FROM companies 
		GROUP BY type 
		ORDER BY typecount DESC LIMIT 5;
```

Note that we introduced a new concept here, specifically the explicit naming of one of the results columns:  `COUNT(id) AS typecount`.

###How many companies of each company type is in bankruptcy? 
The Company database is quite a nicely structured database. For example, it gives us information whether or not a company is in bankruptcy (the bankruptcy column). Let’s first look at how this is distributed.

```
SELECT bankruptcy, COUNT(id) 
	FROM companies 
	GROUP BY bankruptcy;
```

We see that only about 3700 companies in the register are bankrupt.

Do any particular types of company dominate the list of bankrupt companies? We can extend the GROUP BY statement to find out:

```
SELECT type,bankruptcy,count(id) 
	FROM companies 
	GROUP BY type, bankruptcy;
```

This will give us the number of companies in bankruptcy (and those who are not) by category. If we only want to see the count of bankruptcies, we could of course filter on that.

```
SELECT type,COUNT(id) FROM companies
	 WHERE bankruptcy='J' GROUP BY type;
```

### Where are the companies?

Let’s find out where the companies are for a least the most popular cities of registration. We already know how to do this by finding the top types:

```
SELECT city, COUNT(id) 
	FROM companies GROUP BY city;
```

To find the Top 10, we just sort and limit...

```
SELECT city, COUNT(id) AS count 
	FROM companies 
	GROUP BY city 
	ORDER BY count DESC LIMIT 10;
```

Fantastic - now we can start doing some statistics; for example, we can calculate the percentage of companies registered in each city as 100.0 times the count of companies in a city divided by the total number of companies. (We mulitply by *100.0* rather than *100* because otherwise SQLite will perfrom an integer based calculation.) The `ROUND(amount,2)` statement limits the result to two decimal places.

```
SELECT city, COUNT(id) as count,
	ROUND((100.0*COUNT(id))/(SELECT COUNT(id) FROM companies),2) 
	FROM companies 
	GROUP BY city 
	ORDER BY count DESC LIMIT 10;
```

###Which City has the biggest bankruptcy rate?
Although bankruptcies are generally rare - let’s look at which city has the biggest bankruptcy rate.

First we’ll need to find the number of bankrupt companies per city:

```
SELECT city, COUNT(id) AS count
	FROM companies WHERE bankruptcy='J'
	GROUP BY city;
```

We can use this query to create a dummy table that we can merge with another dummy table that contains a count of all the companies in a particular city:

```
SELECT allcos.city,allcos.count,bankrupt.count 
	FROM 
		(SELECT city, COUNT(id) as count
			FROM companies 
			GROUP BY city) AS allcos
		JOIN
		(SELECT city, COUNT(id) AS count
			FROM companies 
			WHERE bankruptcy='J' 
			GROUP BY city) AS bankrupt 
		ON bankrupt.city=allcos.city;
```

The `JOIN` command essentially merges rows from the two separate dummy tables `ON` those rows where the *city* is the same. In the `SELECT` statement we explicitly refer to which original dummy table we want a particular column result to come from.

We can now calculate the bankruptcy rate in each city. Note that if a city hosts a single company and that company is bankrupt, we will get a 100% bankruptcy rate. For this reason, we might look for high bankruptcy rates in cities that are home to more than a specified number of companies in total.

```
SELECT allcos.city, allcos.count, bankrupt.count,
  ROUND((100.0*bankrupt.count)/allcos.count,2)
  	FROM 
  		(SELECT city, COUNT(id) as count 
  			FROM companies 
  			GROUP BY city) AS allcos 
  		JOIN 
  		(SELECT city, COUNT(id) AS count 
  			FROM companies 
  			WHERE bankruptcy='J' 
  			GROUP BY city) AS bankrupt 
  		ON bankrupt.city=allcos.city 
  	WHERE allcos.count> 100 
  	ORDER BY ROUND((100.0*bankrupt.count)/allcos.count,2) DESC 
  	LIMIT 3;
```

## JOINs More Generally

In databases that contain multiple tables, the `JOIN` command can be used to merge data from two or more different tables based on common key values. There are many reasons for designing databases so that different parts of the dataset are kept in different tables, not least to help ensure that the data remains consistent if parts of it are updated. However, a full discussion of them is out of scope for this course and would require several courses in its own right!

Keep an eye out on the School of Data website for future School of Data courses covering *JOINs* and other database analysis techniques.

##Summary
In this second part of our two part course introducing simple databases and the SQL query language, you have seen how we can load in and run a preliminary investigative analysis over quite a large dataset containing over a million entries.

In particular, you have learned how to count various groupings of the data, sorting them and ordering them in order to provide simple league table rankings. In addition, you have learned how to calculate simple percentage based statistics from different counts on the databse.

Finally, you learned how to create some dummy tables, generate summary reports over them and then join them together to provide a report that specified particular event rates.

##Further Reading
Joins are very powerful - Wikipedia has a long [article](http://en.wikipedia.org/wiki/Join_(SQL)) on joins if you need some more nerdy details.
http://schoolofdata.org/2013/03/26/using-sql-for-lightweight-data-analysis/

##Quiz

How would do you identify the number of different cities in the dataset used above? Fill the gap

SELECT _______ FROM companies;

* DISTINCT(city)
* COUNT(DISTINCT(city)) X
* COUNT(city)
* DISTINCT(COUNT(city))

How might you aggregate company counts by city?  (fill the gap)

SELECT city, COUNT(id) AS count FROM companies ________;

* WHERE city = ‘Oslo’
* ORDER BY count ASC
* LIMIT 10
* GROUP BY city X














