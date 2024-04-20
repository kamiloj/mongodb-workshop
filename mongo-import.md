## Cargar la base de datos de pokemons
Descargar [Mongo DB Tools](https://www.mongodb.com/try/download/database-tools)

Descargar el [archivo de pokedex](pokedex.json) 

Ejecutar el comando de importacion

```
mongoimport --host cluster0-primarydb:27017 --db pokedex -c pokemon --type json --file pokedex.json --jsonArray --authenticationDatabase admin --ssl --username username
```


## UI
1. Abrir Data Explorer
Connect
Data Explorer
Open Data Explorer

2. Crear la base de datos
Create Database
DB Name: pokedex
Collection Name: pokemon
Aditional: Select

3. Importar
Insert Document
Select {}
Sobreescribir con JSON


