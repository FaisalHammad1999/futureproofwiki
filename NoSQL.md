NoSQL is a great choice for 'denormalized' data, when you don't anticipate needing to do frequent relationship lookups and where the database may need frequent changes. You can, in fact, both normalize and handle relationships in NoSQL options (the 'No' stands for 'not only'!) however if you you anticipate frequent, complex relationship lookups and minimal change in the format of data, SQL may be a better fit.

You are more likely to find duplicate data in a NoSQL database since the guidelines are, when planning your NoSQL database, to optimise for your most frequent queries rather than data storage. For example, you may store the most recent 'comments' embedded in a post document and have all of the comments stored in a separate comments collection. This would mean duplication but would avoid cross-collection lookups and loading more data in a query than necessary. 

### Implementations
Because of the flexible nature of NoSQL, there are many implementations including:
- **Key Value pair** \
A type of database where all relevant information is stored underneath a key. Common implementations of this include user data, where the user id is the key and the value is all of the relevant user data
- **Document Store** \
Similar to the key-value pair, but with less standardisation as to what is stored in the value. Document Stores also have the idea of collections included. A collection is a group of documents usually organised by nouns. For example we may have a collection of users, made up of documents that contain data about users and another collection of books. An implementation could be data about a patient in a doctors surgery. We might start off with some information e.g history, blood pressure etc., but not all patients will have this information. As patients go for different tests, more results will be added, but as not all patients go for the same tests there may be wild variation in the data stored in the value.
- **Graph databases** \
Graphs are created from collections of nodes and the connections between them. It places as much importance on the pieces of information as the connections between them. That means that we will store information about a specific thing (potentially in document format) but then also will create connections between these documents and store information about those connections. Usual implementations include networks between people, e.g who has sent who an email in a company, or who is managing who.

### Keys
Each type of NoSQL implementation above will have the concept of a key which should be unique. The majority of technical implementations will automatically create a unique key if we do not assign one ourselves.


## MongoDB
Options for NoSQL are many, each offering their own flavour and features. We will look at setup and essential MongoDB (a document store implementation) usage here but the concepts are transferable.

*"MongoDB comes from the word humongous. Our founders built large Internet companies like DoubleClick. They consistently ran into the same problems with databases, one of the biggest problems being scalability. When they set out to build MongoDB, they wanted a database that scaled. Thus, “a humongous database,” or MongoDB."* - [MongoDB FAQs](https://www.mongodb.com/faq)

MongoDB offer several options for running. The Community Server is a great place to start.

## Install
- On [MacOS](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/) it is recommended to install with [homebrew](https://brew.sh/):
    - `brew tap mongodb/brew`
    - `brew install mongodb-community@4.4`
- On [Windows](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/), use the [installer](https://www.mongodb.com/try/download/community) for MongoDB Community Server. When running the installation wizard, if you're not sure, select the option to 'run service as a Network Service User.
- On [Linux](https://docs.mongodb.com/manual/administration/install-on-linux/), find the instructions for your distro [here](https://docs.mongodb.com/manual/administration/install-on-linux/)

**Optional** Install and connect Compass
[MongoDB Compass](https://docs.mongodb.com/compass/current/install/) is one of many options for GUI interaction with your MongoDB service. A GUI is not required but can be a nice way to visualise you data whilst working. You can download it [here](https://www.mongodb.com/try/download/compass?tck=docs_compass). Get the most recent stable, full edition. On MacOS, when you first open Compass, if you receive an error, visit your Security & Privacy > General tab and select 'Open Anyway'.

To connect Compass to your local MongoDB service, select 'Fill in connection fields individually' and hit connect. The default details should be correct but you can check by running `mongo` in your terminal to see the connection details.

***

## Start and Stop the service
To run MongoDB as a background service:
- On MacOS
    - start: `brew services start mongodb-community@4.4`
    - stop: `brew services stop mongodb-community@4.4`
- On Windows use the Services console

***

## Access Mongo shell via the terminal
To interact with your MongoDB service you can access via the command line. Alternatively you can use a GUI such as Compass (see above).
###
- enter: `mongo`
- exit: `exit`

### See all databases
- `show dbs`

### Create a new database / Switch database
- `use <db-name>` ( eg. `use shelter`)
- Use this command to also switch between your dbs

### See current database stats
- `db.stats()`

### Delete a database
- `use <db-to-delete>`
- `db.dropDatabase()`

### Create a new collection
- `db.createCollection(<coll-name>, <options>)`
- eg. `db.createCollection(cats)`
- If you do not need to add any options for eg. max number of documents ('capped') or data validation, then this step is not necessary.
- For more on these options, see the [documentation](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)

### Delete a collection
- `db.<database>.drop()`
- eg. `db.cats.drop()`

### See all collections in a database
- `use <db-to-check>`
- `show collections`

***

## Document CRUD operations


### [**C**reate](https://docs.mongodb.com/v3.4/tutorial/insert-documents/)
- Just one:
    - `db.<collection>.insertOne(<document>)`
    - `db.cats.insertOne({ name: "Zelda", age: 3, owner: "Aki" })`
- Multiple:
    - `db.<collection>.insertMany(<document array>)`
    - eg. `db.cats.insertMany([ { name: "Zelda", age: 3, owner: "Aki" }, { name: "Tigerlily", age: 9 }, { name: "Sam", age: 10, owner: "Bob"} ])`

### [**R**ead](https://docs.mongodb.com/v3.4/tutorial/query-documents/)
- Query documents(s)
    - `db.<collection>.find(<query>)`

There are many query commands, and the [documentation](https://docs.mongodb.com/manual/reference/operator/query/) is an essential resource! Here are a few queries:
- cats older than 7
    - `db.cats.find({age: {$gt: 7}})`
- cats called Zelda
    - `db.cats.find({name: {$eq: "Zelda"}})`
- cats with owners
    - `db.cats.find({ owner: { $exists: true} })`
- cats older than 7 that have owners
    - `db.cats.find({ $and: [ {age: {$gt: 7}}, {owner: {$exists: true}} ]})`
- limit the number of results - cats older than 7 that have owners, max 5 results
    - `db.cats.find({ owner: { $exists: true} }).limit(5)`

When you make a query, a '[cursor](https://docs.mongodb.com/manual/reference/method/js-cursor/)' is returned. 
- See number of documents in a cursor
    - `db.cats.find({age: {$gt: 7}}).count()`
- A cursor can be turned into an json array and stored in a variable
    - `var items = db.cats.find().toArray`
- You can iterate over a cursor directly with its own `forEach`
    - `db.cats.find().forEach(cat => print(cat.name))`
See the documentation for more cursor methods

### [**U**pdate](https://docs.mongodb.com/v3.4/tutorial/update-documents/)
When updating a document, if the field to be updated does not exist, it will be created.
- Update Zelda's age to be 4
    - `db.cats.updateOne({ name: "Zelda" }, { $set: { "age": 4 } })`

- Update all cats older than 2 to have type 'adult'
    - `db.cats.updateMany({ age: { $gt: 2} }, { $set: { "type": "adult" } })`


### [**D**elete](https://docs.mongodb.com/v3.4/tutorial/remove-documents/)
- Delete all documents in collection
    - `db.cats.deleteMany({})`
- Delete first document matching query
    - `db.cats.deleteOne({ name : "Zelda" })`
- Delete all documents matching query
    - `db.cats.deleteMany({ age : { $gt: 6 } })`
***

## Aggregation
[Aggregation](https://docs.mongodb.com/v3.4/reference/method/db.collection.aggregate/) is done in pipeline of actions that can include matching, grouping and more.
Let's make a shopping list to demonstrate:

```js
use db shopping

db.list.insertMany([
    { name: 'hummus', price: 1.99, category: 'snacks' },
    { name: 'poppadoms', price: 1.50, category: 'snacks' },
    { name: 'coffee', price: 11.49, category: 'drinks' },
    { name: 'cider', price: 3.50, category: 'drinks' },
    { name: 'milk', price: 1.99, category: 'drinks' },
    { name: 'tofu', price: 3.50, category: 'fresh' }
])

// calculate total price of all items
db.list.aggregate({$group: { _id: '', total: { $sum: '$price'} }})

// calculate total price of each category
db.list.aggregate({$group: { _id: '$category', total: { $sum: '$price'} }})

// get only items that cost more 2 then calculate price of each category
db.list.aggregate([
    {$match: { price: { $gt: 2} }},
    {$group: { _id: '$category', total: { $sum: '$price'} }}
])

```

***

## Resources
[MongoDB Documentation](https://docs.mongodb.com/manual/) \
[NoSQL Trust Radius](https://www.trustradius.com/nosql-databases)
