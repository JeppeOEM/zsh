# Lecture 11b: Database Systems

This lecture is about databases in web development.

You are not expected to master all these different databases, but you should have a basic knowledge about them, and know when they are useful.

## Database Paradigms

You are already familiar with at least one kind of database paradigm, namely *relational databases*.
These are often referred to as *SQL databases*, but SQL is the language used by most relational databases, not a database paradigm.

You might also have come across other kinds of databases and the term *NoSQL*.
NoSQL is not a database paradigm, however, it covers several distinct database paradigms, and the only thing they have in common is that they are *not* SQL.
Thus, distinguishing between *SQL* and *NoSQL* is like a zoologist dividing the world into *elephants* and *non-elephants*, the latter covering everything from amoebae to human beings.

To learn more about the different database paradigms take a look at Martin Fowler's presentation *[Introduction to NoSQL](https://www.youtube.com/watch?v=qI_g07C_Q5I&list=PLhSvn1XCD2qDL6Bcqx4PI4zpLlBkxrW5C&index=3)*.

The different database paradigms are good a different things, or more precisely, they solve different problems.
In this lecture we will study the most common database paradigms, and the kinds of problems they solve.
It is an essential skill for a web developer to be able to choose the right kind of database for a given task.

## Relational Databases

The relational database paradigm is built upon the mathematical *Set Theory*.
The proper term for a relational database system is *RDBMS*: Relational Database Management System.

|![Set Theory](https://upload.wikimedia.org/wikipedia/commons/thumb/0/04/Set_Theory_Operations.svg/1280px-Set_Theory_Operations.svg.png)|
|:--:|
|*Figure 7.1 - Set Theory from [Wikimedia.org](https://commons.wikimedia.org/wiki/File:Set_Theory_Operations.svg).*|

In basic Set Theory, elements can either be part of a set, or not part of a set.
Figure 7.1 shows *intersection* (top left), *union* (middle left), *symmetric difference* (bottom left), *set difference* (top right), and *exclusion* (middle right) between two sets.
We can operate with any number of sets, and describe any set membership combination with these operations.

If you compare figure 7.1 with figure 7.2, you will see that we can describe the same logic with SQL, only difference here is that we strictly distinguish the *left side* from the *right side*.

Joins are a very powerful tool, but can be rather confusing to get right.
The illustration in figure 7.2 can help you a great deal if you are not sure how to construct a join.

|![SQL Joins](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9d/SQL_Joins.svg/800px-SQL_Joins.svg.png)|
|:--:|
|*Figure 7.2 - SQL Joins are equivalent to memberships in Set Theory. From [Wikimedia.org](https://commons.wikimedia.org/wiki/File:SQL_Joins.svg).*|

A *relation* is not, as many people mistakenly think, a link between elements in two tables.
Rather, it is equivalent to a table in a relational database.

|**Set Theory Lingo** | **RDBMS Lingo**|
|:--------------------|:--------------------|
|Relation             | Table|
|Attribute            | Column|
|Tuple                | Row|
|:--:|
|*Table 7.1 - Terms used in Set Theory vs terms used in RDBMS.*|

A relational database needs a *schema* that defines the tables and columns in the database, constrains, etc.
This means that a bit of planning is needed when using a relational database.

The database schema must also adhere to a *normal form*, to avoid redundancy and optimize for search.
See [Database normalization](https://en.wikipedia.org/wiki/Database_normalization) form more information.

A proper normalized database *gaurantees* that you can query the database for any information, as a combination of data, that is stored in the database, in a very effective and efficient way.

Relational databases use *indexes* to optimize search queries.
Columns that are keys are automatically indexed, but you should make sure that other columns that are used as a criterion in a search query are also indexed.

A wide range of RDBMS are available, both commercially and as free and open source software.
There are small differences between the implementation of the SQL language and the feature set, so one must be careful to choose an RDBMS, especially a commercial RDBMS, as licensing can be extremely expensive.
Luckily one of the best RDBMS is free and open source, namely [PostgreSQL](https://www.postgresql.org).

Using a RDBMS for a project should be considered *the safe choice*.
If you are not sure, or you do not have a very good reason to choose another kind of database system, you should probably choose a RDBMS.


## NoSQL Databases

The following section is not a comprehensive or exhaustive description of databases, but a brief introduction to these databases.
You should do some research to better understand these databases, and to understand when to use them.


### Key/Value Database Systems

Key/Value stores are the simplest of the NoSQL database systems that we are going to look at.
The most popular by far is [Redis](https://redis.io).

As the name implies, this kind of database simply store key/value pairs.
Because Redis can run in-memory, that is not using disk for storage, it is extremely fast, and has very little overhead.

We often use Redis for inter-process communication, i.e., to have two programs communicate with each other, either on the same host or between hosts.
While you could also do this using a socket, Redis allows for asynchronous communication, and works well as a message queue or task queue.

We can also use Redis as a bloom filter, and this usage is so common that there is a ready-to-use implementation available at [RedisBloom](https://oss.redislabs.com/redisbloom/).
A bloom filter is a probabilistic datatype, that we can use for finding out whether we have seen an object before, and thus can retrieve it from cache, or we need to create the object from scratch.
Take a look at [Bloom Filters by Example](https://llimllib.github.io/bloomfilter-tutorial/) for an interactive demonstration.

> **Assignment:** Try the interactive Redis [tutorial](http://try.redis.io/?_ga=2.174736417.220115869.1605706904-1136405265.1605706904).


### Document Based Database Systems

To no surprise document based database systems store data in documents, typically in JSON format.
Documents based systems do not have a schema, so you can store any document regardless of what fields it contain.
This is great for large amount of bulk text, and data that does not adhere to any kind for specification or standard.

Popular implementations are [MongoDB](https://www.mongodb.com) and [CouchDB](https://couchdb.apache.org).
They are very similar from a pure database perspective, but differ in extra features, e.g., CouchDB offers a build-in REST API, MongoDB can be used as a cloud based solution, etc.

Document databases do not have a construct similar to JOIN in SQL.
This means that you either have to store data redundantly, which makes updates complex and expensive, or have references to other documents which means that you must query the database multiple times to perform anything but the simplest of queries.

Document based systems, especially MongoDB, often attracts (web) developers, because they use JavaScript for a query language, and because you can start using the database without any kind of planning, since it is schema-free.
This can be a dangerous strategy, because you might find it impossible to work with in the long run, especially because of complex updates.

Furthermore, simple misspellings can effectively make data disappear, because there is no schema to constrain insertion of data, it might not be possible to find the data unless the same misspelling is used when looking for the data.

```JSON
[
    {
        "name": "John Smith",
        "school": {
            "name": "KEA",
            "address": "Lygten 37",
            "postal_code": "2400",
            "city": "Copenhagen"
        }
    },
    {
        "name": "Jane Doe",
        "school": {
            "name": "KEA",
            "address": "Lygten 37",
            "postal_code": "2400",
            "city": "Copenhagen"
        }
    },
]
```

|:--:|
|*Listing 7.1 - JSON document with redundant data.*|

Listing 7.1 illustrates the redundancy problem.
Because Joins are not available, we must either store the address individually for each student, or reference another document, which means yet another query against the database for *each and every shool*.
Obviously, if the school changes its name or address, each and every instance of that data must be updated.


### Graph Based Database Systems

Graph databases are based on mathematical [Graph Theory](https://en.wikipedia.org/wiki/Graph_theory), not to be confused with *graphs of functions*, also known as *charts*.

Graphs, sometimes known as *networks*, consists of *vertices*, sometimes called *nodes", and *edges*, sometime called *links*.
Many problems can be expressed and analyzed using graphs, so they are a very powerful tool.

|![A Simple Graph](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/6n-graf.svg/1200px-6n-graf.svg.png)|
|:--:|
|*Figure 7.3 - A simple graph.*|

A large number of algorithms work with graphs, and can be applied to graph databases.

Graph databases are useful when connections are in focus.
Say you want to analyze groups of friends or followers on a social network.
While you could model this in a RDBMS, you would get into search queries with many joins back onto the same table.
That is not the case with a graph database, here you can simply follow the links.

Both vertices and edges can have multiple values.

The most popular graph database is [Neo4j](https://neo4j.com).


## Object Relational Mapper

Because we often work with object oriented programming languages in web development, one might think that it would be better using a object oriented database rather than any of the databases we have looked at in this lecture.

However, we do not really have any object oriented databases that measures up to these other kinds of databases.

One way to work around this is to use an *object relational mapper*, i.e., a software component that sits between a relational database and the object oriented language.
This way we can manipulate basic objects and call methods on them like any other object, and the object relational mapper takes care of translating between the two.

Using an object relational mapper has one more advantage: it abstracts away from differences between database implementation.
So even though SQLite and PostgreSQL are not 100% compatible, we can almost entirely ignore that when using an object relational mapper, and we can change the underlying database if we need to.


## ACID and BASE

One important difference between an RDBMS like PostgreSQL and the NoSQL databases is the guarantees they make.

RDBMS are usually *ACID* compliant:

- *Atomic*: all operations in a transaction is either successful or not committed at all (rolled back)
- *Consistent*: each transaction leaves the database structurally intact (you can rely on the database state)
- *Isolated*: transactions are isolated from each other, i.e., they appear to run sequentially
- *Durable*: the effect of a transaction is permanent, even when failures happen

This makes an ACID compliant database system a very reliable system.

NoSQL databases on the other hand are usually *BASE* compliant:

- *Basic Availability*: the database is "always" available
- *Soft-state*: write-consistency is not guaranteed, and nodes may not be mutually consistent
- *Eventual consistency*: the database will eventually be consistent, but may not be at all time

The BASE compliance is obviously a much weaker guarantee, so why would we be interested in that?

The problem is that while ACID compliance is a much stronger guarantee, ACID databases do not scale to the extreme degrees as BASE databases potentially can.
ACID database systems can *scale up*, i.e., faster and bigger server, but they are not good at *scaling out*, i.e., running on many servers.
We refer to this as *vertical* vs *horizontal* scaling.
Horizontal scaling on an RDBMS usually means having a *master* server and a number of *slaves* and replicating changes from the master to the slaves, since only the master server accepts updates, and reading from the slaves.
This comes with serious limitations.

BASE database systems are a product of the Internet.
For many reasons, horizontal scaling is usually preferable in Internet systems, but this comes with the problem of *potential partition*.
A partitioned system can not replicate changes to other nodes, and the system is no longer consistent.
In many cases this is a price worth paying (see the Martin Fowler video at the beginning of this lecture).

## CAP - Consistency - Availability - Partition tolerance Theorem
The idea of CAP theorem is straightforward

- **Consistency** means any client will see the same data no matter which node they connect to (**NOT** the same as ACID consistency)
- **Availability** is a guarantee that there will always be a response provided there are working nodes
- **Partition** **tolerance** means that where there is a communcations breakdown between parts of the system it still functions.

Usually it is the case that there is a choice to be made when a partition happens:

Either the system remains available - but at the risk of some clients seeing a different response (lack of consistency)
Or the system remains consistent but there is a risk of loss of availability (you might not get a valid response)

It was first suggested in detailed form  by Fox and Brewer in this article [](https://ieeexplore.ieee.org/document/798396) for those
who like to read up the detail on these things. The most important point to remember is that with a distributed database you can expect
to be able to choose 2 out of 3 of those elements.

Good descriptions for developers can also be found here:

- [https://www.ibm.com/cloud/learn/cap-theorem](https://www.ibm.com/cloud/learn/cap-theorem)
- [https://neo4j.com/blog/acid-vs-base-consistency-models-explained/](https://neo4j.com/blog/acid-vs-base-consistency-models-explained/)


## Criteria for Choosing a Database System

As you hopefully understand by now, there is no single *perfect* database paradigm or system.
In any project we must analyze the needs, and make informed choices about which database systems to use.
Very often the answer is that we need two or even three different database systems, because this allows us to let the database designs help us solve problems.

A requirement specification is a powerful tool for analyzing the database needs in a project.
Holding the characteristics of database system up against the project requirements should make it clear what kind of database systems will be best suited for the task.

The ACID and BASE guarantees should be part of such an analysis.


## Summary

In this lecture we have looked at different kinds of database paradigms, and a number of implementations of such paradigms.

The most important take-away from this lecture is that you need to analyze your requirements before making decisions about which database system to use.

Often using two or three different database systems is the right choice, because some problems are simply very hard to solve in one kind of database system, while very easy to solve in another.

With this, you should understand that web developers need to be database polyglots.


## Further Reading (Not Mandatory)

If you want to know more about the different database paradigms I can recommend the book *Seven Databases in Seven Weeks, Second Edition* by Luc Perkins (ISBN 978-1-68050-253-4).
The book walks you through a all the databases discussed in this lecture, and a few more, with practical examples.


## Assignments

> **Assignment 11b.1:** Analyze which database systems would be the best choice for **a)** accounts in a bank, **b)** a world-wide e-commerce system, **c)** route planning, **d)** movie recommendation system, **e)** a blog system, **f)** an email system, **g**) Project Gutenberg, **h)** IMDB, **i)** StackOverflow.
