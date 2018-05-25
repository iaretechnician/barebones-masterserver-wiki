**_IMPORTANT NOTE: This wiki document is largely based on personal opinion, and does not necessarily represent the views of anyone other than the author of this specific document (Walt Collins, who is just a member of the MSF community, and not the original author of MSF!). Your own experiences may be different._**

## Choosing a Database Model: RDBMS vs. NoSQL

First of all, you'll need to decide what type of database engine you'll need. That means thinking about the kinds of data you need to manage. Is it easy to think of your data in tables or spreadsheet format, or is it less structured? Here are some insights on your choice: <http://www.zdnet.com/article/rdbms-vs-nosql-how-do-you-pick/>

For applications that have highly structured data, it's usually best to choose a traditional relational database management system (RDBMS). RDBMS databases use indexed tables to store data, and your application can "query" that data using a Structured Query Language (SQL). Examples of popular RDBMS include PostgreSQL, MySQL, SQLite, Microsoft SQL Server (MS-SQL), Oracle, etc. 

If you prefer to work in a more ad-hoc way with your data, without so many pesky rules about data types or required values, you may want to consider a free-form, document-store, NoSQL solution, like MongoDB, LiteDB, or others. <https://db-engines.com/en/article/Document+Stores>

If you decide to use NoSQL then the remainder of this Wiki page will not apply to you. However, if you've chosen to use a RDBMS to hold your data (as I have), then please read on!

## Indie Game Developer's Perspective

As an Indie game developer, you're probably on a very low budget. You'll want to economize wherever you can, but at the same time, you also want to be able to find support when you need it. That means choosing tools that are popular and have large community adoption. This is probably one of the reasons why you've chosen Unity for your game development engine. Choosing the best RDBMS for your project is no different.

Given our low budget, we can pretty much rule out the more expensive options like Oracle and MS-SQL. SQLite offers free licensing, but it's probably not your best choice for a multi-user application (see the SQLite section below for more details). That leaves us looking at the two most popular remaining choices: PostgreSQL and MySQL.

## PostgreSQL vs. MySQL - Major Similarities and Differences:

* **Tools** - both have excellent management tools, including free and paid ones: <https://en.wikipedia.org/wiki/Comparison_of_database_tools>
* **Adoption** - both are used by many large companies and projects: <https://db-engines.com/en/ranking>
* **Licensing**:  
  * PostgreSQL has a liberal open-source, BSD/MIT type license - <https://www.postgresql.org/about/licence/>. It is free to use for any purpose, including commercial.
  * MySQL is free for open source projects that conform to the GPL, but for closed-source projects, MySQL requires the purchase of a proprietary commercial license from Oracle that _**will cost thousands of dollars per year, even when you host it yourself**_. <https://www.mysql.com/about/legal/licensing/oem/>
    * **Note:** From what I understand of the license, if you embed MySQL in your closed source program, you have to buy a commercial license, but to use a MySQL server for your game backend, you should be able to use the free version since your not embedding the MySQL code in your game. (Do your own research)
* **Compliance** to Established ANSI SQL Standards:
  * PostgreSQL is known for its high level of compliance to the standards
  * MySQL has a more proprietary dialect, but still mostly compliant
  * <https://dba.stackexchange.com/questions/110562/which-dbms-are-more-standard-compliant>
* **Common Perceptions**:
  * PostgreSQL is often seen as a "more advanced" and "more reliable" RDBMS
  * MySQL is often thought of as "faster", particularly when using their INNODB engine
  * It's likely that the above are nothing more than perceptions, based on out of date information, and the two systems are technically comparable at this point. In recent versions, PostgreSQL has gotten faster, and MySQL has become more reliable.

## Why not use SQLite for my Main Application Database?

SQLite is a reliable and fast, **server-less** RDBMS. This means that it's run _in-process_ by a single application, and not made to handle the many concurrent reads and writes of a typical multi-user, client-server application's central database. SQLite may be a fantastic choice if you're developing a single-player game. But this Wiki is in support of users of MSF, which is a Unity package that helps you to write multi-user applications.

That being said, SQLite is still an excellent solution for managing a multi-user application's _secondary_ data that can be stored locally, on the user's device. This includes things like game content caches, user configuration/preferences, application temporary update/patch aggregation, and more.

## Conclusions

PostgreSQL and MySQL are similar in many ways, however, MySQL remains more popular by far... about 3 or 4 times more popular. This could be for many reasons, but certainly one major factor is that MySQL is promoted by Oracle as a commercial product. That all being said, PostgreSQL is gaining ground lately in terms of adoption!

PostgreSQL, by comparison, has no significant marketing budget or sales team. It's free! This one simple fact may be enough on its own to persuade you to decide to use PostgreSQL for your indie game development projects, over MySQL.

## Some additional outside references for reading:
* <http://insights.dice.com/2015/12/03/choosing-postgresql-over-mysql-mariadb/>
* <https://en.wikibooks.org/wiki/Converting_MySQL_to_PostgreSQL>
* <https://www.quora.com/What-are-pros-and-cons-of-PostgreSQL-and-MySQL>
* <https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems>