If we think back to the first API we ever made, it did the job but the data was wiped when the server was stopped or restarted. This lack of permanent persistance means any changes we made are lost as soon as we have a server issue - not ideal in real world applications unless you truly don't need that data again in the future.

Now we know how to interact with both MongoDB and PostgreSQL in their respective shells, let's look at how we might query them as part of our API flow. For these examples we are using a small express API. To communicate with our databases via node, we need to `npm install` drivers - [node postgres](https://node-postgres.com/) for Postgres, [mongodb](https://mongodb.github.io/node-mongodb-native/)for MongoDB. As we know, there are a plethora of other databases available to us and each will have a solution. 

You might well take it a step further and install a tool such as [`mongoose`](https://mongoosejs.com/) or [`sequelize`](https://sequelize.org/) to give you a hand with the access and mapping of the returned data. This is certainly worth a good look for an app any more complex than this example but first we want to make sure that we have an idea what is going on beneath the surface of these ODM/ORM tools.
  
These solutions could be used with any client interchangeably.

## Mongo
- Install [mongodb](https://mongodb.github.io/node-mongodb-native/) `npm install mongodb`
### Initialise your connection
```js
const { MongoClient, ObjectId } = require('mongodb')

const connectionUrl = process.env.CONNECTIONSTRING
const dbName = process.env.DATABASENAME

let db;

const init = () => {
  return MongoClient.connect(connectionUrl).then((client) => {
    db = client.db(dbName)
    console.log('connected to database!', dbName)
  })
}
```
### Craft a query
```js
const create = newDog => {
  const collection = db.collection('dogs')
  return collection.insertOne(newDog)
}
```

### Call your query from a route!
```js
router.post('/', (req, res) => {
    const newDog = {name: req.body.name, age: parseInt(req.body.age)}
    create(newDog)
        .then(result => {
            const dog = { id: result.ops[0]._id, name: result.ops[0].name, age: result.ops[0].age }
            res.status(201).json(dog)
        })
        .catch(err => res.status(500).end())
})
```


## PostgreSQL
- Install [node postgres](https://node-postgres.com/) `npm install pg`

### Prepare your connection
```js
const { Pool } = require("pg");

const pool = new Pool({ database: process.env.PGDATABASE})) // You can add additionl options here to specify host, password, etc. It will default to the standard pg localhost port.

function run(q, values, callback){
    return pool.query(q, values, callback)
}
```

### Craft a query
```js
const create = `INSERT INTO dogs (name, age) VALUES ($1, $2) RETURNING *`;
```

### Call your query from a route!
```js
router.post('/', (req, res) => {
    db.run(create, [req.body.name, req.body.age])
        .then(resp => {
            const dog = resp.rows[0]
            res.status(201).json(dog)
        })
        .catch(err => res.status(500).end())
})
```

***


## Environment variables
As we start working more with databases, there's a a good chance that we will be needing to store some sensitive information. We want to make sure that things like the password for a secured database are not shared, for example on GitHub. [`dotenv`](https://www.npmjs.com/package/dotenv) is a simple and excellent library for this purpose.