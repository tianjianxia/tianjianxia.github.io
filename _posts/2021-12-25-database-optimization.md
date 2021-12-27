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

Most of popular relational database use data structures like B+ tree or red black tree to implement database indexing. A balanced tree structure like red black tree has some advantages like everytime you access a individual node of the tree, the expected average searching time will be similiar. This stands that the structure helps the data querys "divide and conquer" the entries. A SQL WHERE statement will go either left or right when it excutes on a tree node entry(Some structure like B+ tree will be different because it has multiple children). The tree struct help you query in a even time, but it pay a price. When you try to delete or update a node value, it will trigger the tree structure to alter itself to meet the query requrement(value balanced). The insert, update and delete process in an indexed table is more exhausting than a normal one. More detailed informations about database indexing and balanced tree algorithms, please see [this](www.google.com).

<br/>

- Add a cache service for the database

<br/>

- Split the table into a read table and a write one

<br/>

- Partition the table horizontally or vertically

<br/>

- Implement a database connection pool

<br/>