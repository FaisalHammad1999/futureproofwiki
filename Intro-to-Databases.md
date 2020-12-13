### What is a Database?

Simply put a database is a collection of structured information generally held and accessed electronically.

### Who would use one?

Perhaps an easier question to answer is who wouldn't use one? Storing data is an important part of many modern businesses, particularly the applications they use and develop. Think banking, retail, social media... the list is endless.

### Why use Databases?

The main reasons for using databases are that they allow us to retain information, which we can access, manage, update and organise. This could mean user information, stock availability or your latest social post.

### Where is a database stored?

Information can be stored in a local database on your personal computer but as the data grows this becomes impractical and counter productive. Most databases are stored away from local machines in data centres, which are essentially large rooms or warehouses with servers. These could be owned and managed by a company, provided via cloud platforms such as AWS, GCP or Azure or a hybrid of both.

### How can you access one?

If your database is not stored locally, you can access it via the internet by making a request to the server for information which in turn is relayed back. At the very minimum a database should require an access key or token to make these requests.

### When can you access the data?

Ideally you should be able to access data at any time, however as you can imagine storing large amounts of information can become expensive due to the space and servers required to hold this. Generally you either have hot storage or cold storage. Databases held in hot storage can be reasonably expected to be instantly accessible but expensive to run, whereas getting data from cold storage may take longer but is less expensive.

## Types of Databases

### Relational

Traditionally when we think of databases we often think of tables of data with headers, columns and rows. 

This is called a relational database - one where we can define set categories, put the data into established tables and define the relationship between them. Each table will represent a specific 'entity type', for example: users, orders and products. The reason for this is to make sure there is a separation of concerns between tables.

Each table will have:

* Headers known as **Attributes**
* Rows named **Records**
* A unique ID called a **Key**

![Relational Model](https://i.imgur.com/G6f18yM.png)

With a relational database we can place constraints on an attribute to restrict the type of data that we are storing, for example ensuring height is stored as a number and restricting it to be between two realistic figures. We can also put a constraint in place to say if a piece of data is optional or not.

We put constraints in place to ensure that we maintain high quality data which will likely be easier to maintain over time.

The language used for accessing data in a relational database is called **SQL**.

Examples of SQL databases are [SQLite](https://www.sqlite.org/index.html), [PostgreSQL](https://www.postgresql.org/) and [MySQL](https://www.mysql.com/).

### Non-Relational

Relational databases are very useful _if we are certain of the format of the data_. However not all data will fit nicely into that kind of structure which has given rise to the non-relational database.

A common type of non-relational database is a document data store, which looks a lot like a JavaScript object, typically stored in JSON. There is no mandatory way that each document is created, allowing for flexibility depending on the data we are storing. Much like a relational database though, each document will have a key so that it can be accessed uniquely.

![Non-Relational Model](https://i.imgur.com/5yMWUiY.png)

Non-relational databases are often referred to as **NoSQL**.

Examples of NoSQL databases are [MongoDB](https://www.mongodb.com/) and [CouchDB](https://couchdb.apache.org/).

## Scale

When thinking about scaling, we do so in reference to different axis.

Relational databases are often scaled vertically in the first instance, meaning if we up the power of our hardware we can process and store more data. This would mean upgrading or increasing the CPU, RAM, etc. This can become expensive/unfeasible at a point though. 

Non-relational databases are typically horizontally scalable, meaning if we increase the number of servers we can process and store more data. Non-relational databases lend themselves better to this kind of scaling as they are more easily able to be broken up into multiple pieces know as sharding. Relational databases can be broken up but may mean a lot more work for the developers as they will likely be required to write logic to handle the sharding.

Find out more about scale [here](https://medium.com/better-programming/scaling-sql-nosql-databases-1121b24506df).

## Pros and Cons

The main pro of a relational database is the structure. We know what to expect in terms of the data and because of this can use more complicated queries than a non-relational database where we are unsure of what is stored within it. As mentioned above we can also set constraints so that when inputing data, we can achieve consistency. SQL databases are best fit for transactional applications.

Non-relational databases are useful for hierarchical data storage and big data as it's easier to scale and unstructured. It's more dynamic than an SQL database when it comes to changing requirements.

## Designing a Database

When we design a database we need to first think about what we want to store and what we will need access to. Once this has been completed we can begin to consider relational vs. non-relational databases. When we have decided on this we can begin to create a schema, which in the case of a relational database will consist of table names, attributes and constraints. In the case of a non-relational database we can start to consider what might be inside of a singular document, if there will be groups of documents and what these groups would be called.

## Security

It is always important when we discuss data and databases to consider security. We should be considering if we need to store specific data and if we have the appropriate protection in place. Examples of this would be not storing plain-text passwords, encrypting your data and requiring tokens to access a database.

### ACID Transactions
ACID is an acronym you may see a lot when comparing various database offerings. A 'transaction' is any interaction with the database. The acronym stands for:
- **A**tomicity:
If any part of the transaction fails, the whole process will roll back. No half finished transactions allowed!
- **C**onsistency:
The transaction takes the database from one valid 'state' to another. No invalid resulting databases allowed!
- **I**solation:
No transaction can mess up another transaction. No interfering with other transactions allowed!
- **D**urability:
Once written, the transaction will persist. No suddenly missing data allowed!