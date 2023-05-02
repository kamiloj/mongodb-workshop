
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
