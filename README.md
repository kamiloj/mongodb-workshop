# MongoDB  Workshop
Hoy tendremos la oportunidad de explorar la base de datos MongoDB en nube.

para ello seguiremos las guias como se presentan aqui

1. Crear una base de datos de capa gratuita
[Link](create-mongo-db.md)

2. Cargar la tabla documental
[Link](mongo-import.md)

3. Workshop asistido
[Link](mongo-import.md)

4. Consultas adicionales
[Link](mongo-import.md)





#  Intro

MongoDB is a popular, easy-to-use database. It is a NoSQL database which stores JSON-like documents. This workshop will cover the following points:

- What MongoDB is
- How to install MongoDB
- How to create, read, update, and delete data in MongoDB
- How to use MongoDB with NodeJS


## What is MongoDB?

MongoDB is a NoSQL database which stores JSON-like documents. What do each of these mean?

- Database
- NoSQL
- JSON-like documents

### What does NoSQL mean?

MongoDB is a very modern type of database. Older databases such as MySQL and Postgres are known as _relational databases_. The Relational Model is more than 35 years old, and it's the foundation of database management systems. The database holds a set of relations, or tables.

You can think of these tables as spreadsheets. Each table is made up of columns and rows. Each row represents one record in the table.

Table: People

| id | name    | age | location      |
|---:|---------|----:|---------------|
| 1  | Ash     | 10  | Pallet Town   |
| 2  | Misty   | 10  | Cerulean City |
| 3  | Brock   | 15  | Pewter City   |

Table: Pokemon

| id | name       | type     | owner_id |
|---:|------------|----------|---------:|
| 1  | Pikachu    | Electric | 1        |
| 2  | Charmander | Fire     | 1        |
| 3  | Staryu     | Water    | 2        |
| 4  | Starmie    | Water    | 2        |
| 5  | Onyx       | Rock     | 3        |

These relational databases nearly always use a language called SQL (Structured Query Language) to retrieve information from the database. That's why relational databases are often just called _SQL databases_.

Here are some example SQL queries on the data shown above:

Query:

```sql
SELECT name, type
FROM pokemon
WHERE owner_id = 1;
```

Output:

|       name |     type |
|------------|----------|
|    Pikachu | Electric |
| Charmander |     Fire |

Query:

```sql
SELECT pokemon.name AS 'Pokemon Name', people.name AS 'Trainer Name', people.location
FROM people
LEFT JOIN pokemon
ON pokemon.owner_id = people.id
WHERE people.location IN ('Pallet Town', 'Cerulean City')
```

Output:

| Pokemon Name | Trainer Name |      location |
|--------------|--------------|---------------|
|      Pikachu |          Ash |   Pallet Town |
|   Charmander |          Ash |   Pallet Town |
|       Staryu |        Misty | Cerulean City |
|      Starmie |        Misty | Cerulean City |


#### The problem with SQL: Object Relational Mapping

One of the most frustrating things about relational databases is that programming languages don't really want to work with flat tables. They want to work with rich objects. This means you have to map the tables in your relational database to the objects you use in your code. This is called _Object Relational Mappting_ (ORM). This adds complexity to your application, is time consuming, hard to maintain, and gets more difficult as your application grows. ORM has been such a pain over the years, that some programmers have even called it "[the Vietnam of Computer Science](https://blog.codinghorror.com/object-relational-mapping-is-the-vietnam-of-computer-science/)"!


#### MongoDB and NoSQL

Instead of storing data in flat tables, MongoDB stores data as JSON objects (or _documents_). This is a different approach to relational databases so it's called _NoSQL_. MongoDB also uses its own query language instead of SQL. But it's mostly called NoSQL to refer to the fact it doesn't store data as tables.

This is fantastic because now you no longer need ORM. If you us a language that has objects, you can send an object directly to the database and read it out again without having to do any complex mapping.

Relational databases are still very useful for many things. But NoSQL databases such as MongoDB now offer a quicker and easier way to add a database to your application.


### What is JSON?

_JavaScript Object Notation_ (JSON) is a simple data format based on JavaScript object literals. An object is enclosed in a pair of curly braces (`{}`) and contains a list of key-value pairs. Keys are strings. Values can be strings, numbers, booleans, arrays of values, or even other objects.
	
Here's an example JSON document:

```JSON
{
	"id": 1,
	"num": "001",
	"name": "Bulbasaur",
	"img": "http://www.serebii.net/pokemongo/pokemon/001.png",
	"type": [
		"Grass",
		"Poison"
	],
	"height": "0.71 m",
	"weight": "6.9 kg",
	"candy": "Bulbasaur Candy",
	"candy_count": 25,
	"egg": "2 km",
	"spawn_chance": 0.69,
	"avg_spawns": 69,
	"spawn_time": "20:00",
	"multipliers": [1.58],
	"weaknesses": [
		"Fire",
		"Ice",
		"Flying",
		"Psychic"
	],
	"next_evolution": [{
		"num": "002",
		"name": "Ivysaur"
	}, {
		"num": "003",
		"name": "Venusaur"
	}]
}
```

MongoDB stores data as a type of JSON called BSON (_Binary JSON_). BSON adds more types of values (such as dates, different types of integers and floats, binary data, and more). It's useful to know MongoDB documents aren't quite JSON, but in general you won't see much of a difference.

Here's an example of what the SQL database shown above might look like as a collection of objects in MongoDB:

```JSON
{
	"id": 1,
	"name": "Ash",
	"age": 10,
	"location": "Pallet Town",
	"pokemon": [
		{ "id": 1, "name": "Pikachu", "type": "Electric" },
		{ "id": 2, "name": "Charmander", "type": "Fire" }
	]
}

{
	"id": 2,
	"name": "Misty",
	"age": 10,
	"location": "Cerulean City",
	"pokemon": [
		{ "id": 3, "name": "Staryu", "type": "Water" },
		{ "id": 4, "name": "Starmie", "type": "Water" }
	]
}

{
	"id": 3,
	"name": "Brock",
	"age": 15,
	"location": "Pewter City",
	"pokemon": [
		{ "id": 5, "name": "Onyx", "type": "Rock" }
	]
}
```

We've made the decision to store Pokemon belonging to each trainer in an array on each trainer document. This kind of nesting is difficult to get in relational databases. This makes it very easy if you want to get a trainer and all their Pokemon. But you have to put some thought into how you want to structure your documents. For example, this structure might cause some issues if you need to add a Pokemon which is owned by two trainers (such as Meowth).

![Jesse and James cuddling the Pokemon Meowth](./meowth.gif)


