
## Using the Mongo Shell

Run the Mongo shell to interact with your database:

```
$ mongosh "mongodb+srv://clustername.random.mongodb.net/myFirstDatabase" --apiVersion 1 --username user

```


The Mongo shell is a javascript REPL which has built-in commands for communicating with a MongoDB server. Test it out by entering some JavaScript code:

```
> double = x => x*2
x => x*2
> double(4)
8
```

You can view some basic commands by entering the `help` command:


```
> help
		db.help()                    help on db methods
		db.mycoll.help()             help on collection methods
		sh.help()                    sharding helpers
		rs.help()                    replica set helpers
		help admin                   administrative help
		help connect                 connecting to a db help
		help keys                    key shortcuts
		help misc                    misc things to know
		help mr                      mapreduce

		show dbs                     show database names
		show collections             show collections in current database
		show users                   show users in current database
		show profile                 show most recent system.profile entries with time >= 1ms
		show logs                    show the accessible logger names
		show log [name]              prints out the last segment of log in memory, 'global' is default
		use <db_name>                set current database
		db.foo.find()                list objects in collection foo
		db.foo.find( { a : 1 } )     list objects in foo where a == 1
		it                           result of the last line evaluated; use to further iterate
		DBQuery.shellBatchSize = x   set default number of items to display on shell
		exit                         quit the mongo shell
```

Let's test out some of these commands. List all the databases on the MongoDB server with this command:

```
> show dbs
admin		0.000GB
local		0.000GB
```

A new MongoDB server only has two databases, the `admin` and `local` databases. These are for use by MongoDB. You generally don't want to use these.

Each _database_ is made up of _collections_ of _documents_. The current database is available in the `db` varibale. By default it is set to `test`:

```
> db
test
```

This doesn't show up when you run `show dbs` because there aren't any documents in the `test` database yet. Databases and collections are created lazily when you insert documents. Let's insert a document into the `people` collection in the `test` database:

```
> db.people.insertOne({ name: "Misty", age: 10, location: "Cerulean City" })
{
    "acknowledged" : true,
    "insertedId" : ObjectId("5a861440faab325a3b3326a6")
}
```

This will create the `test` database, the `people` collection inside the `test` database, and the document for Misty inside the `people` collection.

See the current databases:

```
> show dbs
admin   0.000GB
local   0.000GB
test    0.000GB
```

And the collections in the current database:

```
> show collections
people
```

To show all the documents in a collection, use the `find` method:

```
> db.people.find()
{ "_id" : ObjectId("5a861440faab325a3b3326a6"), "name" : "Misty", "age" : 10, "location" : "Cerulean City" }
```

Collections are properties on the `db` object. Each collection has methods for manipulating documents in the collection: finding, inserting, updating, deleting, counting, and more. You can see a full list of methods on a collection by running `db.people.help()`.

Here's another method to count the number of documents in a collection:

```
> db.people.count()
1
```

### The `_id` field

You'll notice that when we inserted this document:

```
{ name: "Misty", age: 10, location: "Cerulean City" }
```

This was the document we found in the database:

```
{ "_id" : ObjectId("5a861440faab325a3b3326a6"), "name" : "Misty", "age" : 10, "location" : "Cerulean City" }
```

MongoDB adds an `_id` property to each inserted document. The value of `_id` is a unique identifier for the object called an ObjectId. The `_id` field is indexed which means it's very quick to search for documents by `_id`. You can set the `_id` manually, but it's best to let MongoDB handle it.

The ObjectId is a 24-character hex string. You'll notice it's wrapped in `ObjectId()`. This is a special BSON type. You can read more about ObjectIds in the [ObjectId documentation](https://docs.mongodb.com/manual/reference/bson-types/#objectid).

## Workshop

Connect to pokedex db

```
> use pokedex
```

The `db` variable is already set to your database:

```
> db
pokedex
```

Now we can view the documents in the `pokemon` collection:

```
> db.pokemon.find().pretty()
```

The `.pretty()` method will output the documents in an easily readable format. There are 151 documents in the collection, but you'll notice the shell only printed 20 of them, then said this:

```
Type "it" for more
```

This is because queries don't return documents directly, they return a cursor. Then you iterate over the cursor to return documents. This is so that, if you have a million documents in a collection, you don't return them all at once which could seriously hamper your application's performance. The Mongo shell iterates over cursors in 20 document batches. Most drivers in programming languages have batches of 101 documents. The batch size can vary depending on a lot of things, it's just something to be aware of.

Iterate to the next batch of documents by typing `it`:

```
> it
```

You can pass a _filter document_ to the `find()` method to filter the documents by their properties. This command will return all documents where the `name` is `"Charizard"`:

```
> db.pokemon.find({ name: "Charizard" }).pretty()
```

This will return all documents where the `height` is `"1.70 m"`:

```
> db.pokemon.find({ height: "1.70 m" }).pretty()
```

You can see that this returns 6 Pokemon (Charizard, Golduck, Victreebel, Rapidash, Dewgong, and Articuno).

You can also drill into document properties using the dot notation that is common in JavaScript objects (See [here](https://docs.mongodb.com/manual/core/document/#dot-notation) for more information.)

There are many special _query operators_ which start with `$` signs that can be used to locate documents. Here we use the `$gt` operator to find all Pokemon with a weight more than `"90.0 kg"`:

```
> db.pokemon.find({ weight: { $gt: "90.0 kg" } }).pretty()
```

This returns just two Pokemon (Charizard and Rapidash).

To find the rarest Pokemon, we can use the `$lt` operator. This will return the Pokemon that have a spawn chance of less than 0.1%:

```
> db.pokemon.find({ spawn_chance: { $lt: 0.001 } }).pretty()
```

There are only 6 Pokemon that are this rare (Ditto, Articuno, Zapdos, Moltres, Mewtwo, and Mew).

There are operators for nearly any type of filtering you may want to do. Read the [Query and Projection Operators](https://docs.mongodb.com/manual/reference/operator/query/) documentation to learn about what's available. Here are a few examples you may find useful:

Find all Pokemon that have only one type (checks that the `type` array has only 1 element):

```
> db.pokemon.find({ type: { $size: 1 } }).pretty()
```

Find all Pokemon that are in their final evolution state (checks for the documents that don't have a `next_evolution` property):

```
> db.pokemon.find({ type: { $all: ["Water"] } }).pretty()
```

Find all the Pokemon that are both water type and less than 1.00 m tall:

```
> db.pokemon.find({ $and: [ { type: "Water" }, { height: { $lt: "1.00 m" } } ] }).pretty()
```


### Counting

You can count the number of documents that match a _filter document_ by using the `count` method on a collection:

```
> db.pokemon.count()
151
> db.pokemon.count({ height: "1.70 m" })
6
```

Or, instead of appending `.pretty()` to `find` queries, you can append `.count()`:

```
> db.pokemon.find({ height: "1.70 m" }).count()
6
```


### Projection

If you don't want to return all the properties in a document, you can use a _projection document_ as the second argument to the `find` command to only show certain arguments. For example, to only show the name and weight of Pokemon 1.7m tall:

```
> db.pokemon.find({ height: "1.70 m" }, { name: true, weight: true }).pretty()
{
        "_id" : ObjectId("5a8744754c24cb5c863ed309"),
        "name" : "Charizard",
        "weight" : "90.5 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed33a"),
        "name" : "Golduck",
        "weight" : "76.6 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed34a"),
        "name" : "Victreebel",
        "weight" : "15.5 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed351"),
        "name" : "Rapidash",
        "weight" : "95.0 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed35a"),
        "name" : "Dewgong",
        "weight" : "120.0 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed393"),
        "name" : "Articuno",
        "weight" : "55.4 kg"
}
```

The `_id` field is always shown unless you explicitly ask for it not to be shown:

```
> db.pokemon.find({ height: "1.70 m" }, { _id: false, name: true, weight: true }).pretty()
{ "name" : "Charizard", "weight" : "90.5 kg" }
{ "name" : "Golduck", "weight" : "76.6 kg" }
{ "name" : "Victreebel", "weight" : "15.5 kg" }
{ "name" : "Rapidash", "weight" : "95.0 kg" }
{ "name" : "Dewgong", "weight" : "120.0 kg" }
{ "name" : "Articuno", "weight" : "55.4 kg" }
```


### Update

To update a single document, you can use the `updateOne` command. First, let's look at the document for Psyduck:

```
> db.pokemon.find({ name: "Psyduck"}, { name: true, height: true }).pretty()
{
        "_id" : ObjectId("5a8744754c24cb5c863ed339"),
        "name" : "Psyduck",
        "height" : "0.79 m"
}
```

Now we'll use the `$set` operator to change the height of Psyduck to 10 meters. Scary!

```
> db.pokemon.updateOne({ name: "Psyduck"}, { $set: { height: "10.0 m" } })
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

The output tells us that the update found one document and modified one document. Let's check if the record was modified:

```
> db.pokemon.find({ name: "Psyduck"}, { name: true, height: true }).pretty()
{
        "_id" : ObjectId("5a8744754c24cb5c863ed339"),
        "name" : "Psyduck",
        "height" : "10.0 m"
}
```

If you want to update multiple documents at once, use the `updateMany` command. This will increment the average spawns of the two heaviest Pokemon by 1:

```
> db.pokemon.updateMany({ weight: { $gt: "90.0 kg" } }, { $inc: { avg_spawns: 1 } })
{ "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 2 }
```

### Delete

To delete documents, you can use `deleteOne` or `deleteMany`. For example:

```
> db.pokemon.deleteOne({ name: "Mewtwo" })
{ "acknowledged" : true, "deletedCount" : 1 }
```


## Indexing and Performance

Indexes are vital to understand if you want your database queries to be fast. If you search for documents using an unindexed field such as `name`, MongoDB will have to go through every single document on the disk and check the value of `name` to return the results.

```

> db.pokemon.find({ name: "Mew" }).explain("executionStats")
```

The `explain` command will give you information about how a query is executed. We see in the execution stats that the `totalDocsExamined` were 150! Every document in the collection! If you have thousands or millions of records, this will get unbearably slow!

If you index a field, MongoDB will order them and put them in a data structure (a B-tree) which will make it very, very quick to find values. This index is smaller than all the documents, so it can fit in memory, making tree-traversal lightning fast compared to having to read documents from the disk.

Indexing is a deep and nuanced subject. If you start to run production apps, you should read and fully understand [the indexing documentation](https://docs.mongodb.com/manual/indexes/). For now, here's how to index a single field:

```
> db.pokemon.createIndex({ name: 1 })
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

Now, running `explain` on the previous command:

```
> db.pokemon.find({ name: "Mew" }).explain("executionStats")
```

We can see that the `totalDocsExamined` is 1. MongoDB was able to immediately pick out the answer to our query. This is much more efficient.


## Aggregation Framework

You can create even more complex queries by using the _Aggregation Framework_. These are a powerful series of operations you can perform to manipulate your data.

For example, this will select a random Pokemon:

```
> db.pokemon.aggregate([{ $sample: { size: 1 } }])
```

This will group Pokemon by their type and show how many Pokemon of each type there are:

```
> db.pokemon.aggregate([{ $group: { _id: "$type", count: { $sum: 1 } } }])
```

You can delve deeper into aggregations in the [documentation](https://docs.mongodb.com/manual/aggregation/).


## MongoDB drivers

If you want to talk to a MongoDB server from your application, you need to use a driver. There are drivers for every language you could need: Java, Python, NodeJS, PHP, C#, and dozens more.

You can find the driver for your language here: https://docs.mongodb.com/ecosystem/drivers/

Each driver will come with a guide for getting started. mLab have also compiled examples for many drivers here: https://github.com/mongolab/mongodb-driver-examples
