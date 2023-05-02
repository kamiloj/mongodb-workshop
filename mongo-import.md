## Cargar la base de datos de pokemons
Descargar [Mongo DB Tools](https://www.mongodb.com/try/download/database-tools)

Descargar el [archivo de pokedex](pokedex.json) 

Ejecutar el comando de importacion

```
mongoimport --host cluster0-primarydb:27017 --db pokedex -c pokemon --type json --file pokedex.json --jsonArray --authenticationDatabase admin --ssl --username username
```
