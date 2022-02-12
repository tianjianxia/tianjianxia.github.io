---
layout: post
title: How to optimize a SQL table with massive data
tags: [SQL Server, database optimization]
---


<br/>

This is a new topic about how to deal with a very large datatable. During my work in a fintech company, my team is facing some issues about several SQL Server table with large number of rows. Specific issue include large-scale query slow and database I/O resources tight. So I would like to start this topic, and analyse what cause the problem and how to opt them. I will introduce many scenarios in or out of my production context, and I will talk about how I solve my problem about the exact problem. 

The article is mainly about database indexing concept, database caching, read-write splitting, database partitioning and databse connection pool.

<br/>

***

<br/>

#### Problem we are facing

Imagining you have a table with more than 1 billion rows, and in most of scenarios the requirement need to query almost half of the data from table. You try to batch your SQL query statement, and you add index to the datatable. But, still, the query process takes a lot of time, and the database server I/O resource is fully occupied.

How would you do with this?

<br/>

- Add index to the table

<br/>

If you are a software engineer working on server end, even you are not very professional in relational database, you will know something about table indexing. Every programmer knows how to create a table will know how to create a index for the table. But not exact everyone knows why index will help the table querys faster and in what situation should we add index to a table.

Most of popular relational database use data structures like B+ tree or red black tree to implement database indexing. A balanced tree structure like red black tree has some advantages like everytime you access a individual node of the tree, the expected average searching time will be similiar. This stands that the structure helps the data querys "divide and conquer" the entries. A SQL WHERE statement will go either left or right when it excutes on a tree node entry(Some structure like B+ tree will be different because it has multiple children). The tree struct help you query in a even time, but it pay a price. When you try to delete or update a node value, it will trigger the tree structure to alter itself to meet the query requrement(value balanced). The insert, update and delete process in an indexed table is more exhausting than a normal one. More detailed informations about database indexing and balanced tree algorithms, please see [this](https://medium.com/trendyol-tech/sql-server-index-architecture-b320999547c4).

<br/>

- Add a cache service for the database

<br/>

If the querying speed of the database is not fast enough(I am talking about hard-drive based databse like MySQL). We can try to migrate some of the data in the hard disk to the physical memory, since the memory speed is significantly faster than hard disk. The way to implement this can either using thrid-party in-memory database like Redis and Memcached, or we can try to develop a in-memory service to hold all the queryable objects in the process, like a memory consuming web services. A typical example is [this](https://github.com/hyperoslo/Cache)

<br/>

- Partition the table horizontally or vertically

<br/>

By part a table into multiple table, we can make query faster. Because the way SQL engine handle a SQL statement will be optimized by itself. Take "Select * from A WHERE ID IN (1, 2, ..., 10000)" as an example, when the engine try to run this query, it will first batch the query into multiple pieces, tearing 1 tasks ranging from ID 1 to 10000, into 10 tasks: 1 to 1000, 2 to 2000, ... etc. This kind of strategy ensure the engine can dismanting a huge statement into small transactions and run them simultaneously. Originally, when we try to run a large statement on a single table, the task in batched and run syncronously, this will lead to the inefficency in I/O and query queueing. But if we seperate the table, do the query on multiple table, the query process will be more easy to batch, the complexity of the query statement will be reduced, thus make the query faster. 

A partition in SQL table can be horizontally or vertically. Horizontal partition means rows of a table are teared apart into multiple table by a specific column value. Like all the student in a school are batched using their first character of their last name. This will make a select all statement faster. Vertical partition means rows remain the same, but a table is divied into smaller ones with less columns. Like a student table are divided into student name table and student grade table. This will make a select column in specific column faster. Both these partition will make some of the query faster, but they will lead to extra space complexity.

<br/>

- Implement a database connection pool

<br/>

Add a connection pool to a database will precisely manage the I/O resource of the database. The fact is connection to a database can be expensive. A time-consuming query statement will seize amount of I/O resouces(either TCP socket or hard disk I/O) and make other query task lack of I/O. This will lead to situations like query time-out, database lagging or even database server down. So involving a connection management strategy will benefit the services. Again, there are two ways to add connection pool to the database. One, implement an additional service layer by yourself to monitor all the connection thread to the database. Two, using third party library like [HikariCP](https://github.com/brettwooldridge/HikariCP) and [Druid](https://druid.apache.org/)

<br/>