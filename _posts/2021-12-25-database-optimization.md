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

- Add a cache service for the database

<br/>

- Split the table into a read table and a write one

<br/>

- Partition the table horizontally or vertically

<br/>

- Implement a database connection pool

<br/>