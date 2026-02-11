# Workshop MongoDB: Modelos de Datos Document Store

## 📋 Información del Workshop

**Curso:** Procesamiento de Datos Distribuidos  
**Clase:** Modelos de Datos, Lenguajes de Consulta y Procesamiento  
**Duración estimada:** 2 horas  

---

## 📚 Conexión con la Clase

Este workshop pone en práctica los conceptos teóricos que vimos en clase:

| Concepto de Clase 3 | Aplicación en este Workshop |
|---------------------|------------------------------|
| **Document Stores** | Exploración de documentos JSON en MongoDB |
| **Embedding vs Referencing** | Análisis de evoluciones de Pokemon (embebidas vs referencias) |
| **Estructuras de datos (B-Trees)** | Creación y análisis de índices |
| **Trade-offs de consistencia** | Operaciones CRUD y comportamiento de MongoDB |
| **Patrones de acceso** | Queries optimizadas para lectura vs escritura |
| **Aggregation** | Pipeline de transformación (similar a MapReduce) |

---

## 🚀 Parte 1: Conexión y Exploración Básica

### 1.1 Conectar a la Base de Datos

Primero, nos conectamos a la base de datos `pokedex`:

```javascript
use pokedex
```

**Salida esperada:**
```
switched to db pokedex
```

**📖 Explicación:**  
El comando `use` en MongoDB cambia el contexto de la shell al database especificado. Si el database no existe, MongoDB lo creará automáticamente cuando insertemos el primer documento.

---

### 1.2 Verificar la Conexión

Verificamos que estamos conectados correctamente:

```javascript
db
```

**Salida esperada:**
```
pokedex
```

**📖 Explicación:**  
La variable `db` es una referencia global en la MongoDB shell que apunta al database actual. Esto nos permite ejecutar operaciones como `db.collection.find()`.

---

### 1.3 Ver los Documentos en la Colección

Ahora exploramos los documentos en la colección `pokemon`:

```javascript
db.pokemon.find().pretty()
```

**Salida esperada (primeros 2 documentos):**
```javascript
{
    "_id" : ObjectId("5a8744754c24cb5c863ed308"),
    "id" : 1,
    "num" : "001",
    "name" : "Bulbasaur",
    "img" : "http://www.serebii.net/pokemongo/pokemon/001.png",
    "type" : [
        "Grass",
        "Poison"
    ],
    "height" : "0.71 m",
    "weight" : "6.9 kg",
    "candy" : "Bulbasaur Candy",
    "candy_count" : 25,
    "egg" : "2 km",
    "spawn_chance" : 0.69,
    "avg_spawns" : 69,
    "spawn_time" : "20:00",
    "multipliers" : [
        1.58
    ],
    "weaknesses" : [
        "Fire",
        "Ice",
        "Flying",
        "Psychic"
    ],
    "next_evolution" : [
        {
            "num" : "002",
            "name" : "Ivysaur"
        },
        {
            "num" : "003",
            "name" : "Venusaur"
        }
    ]
}
{
    "_id" : ObjectId("5a8744754c24cb5c863ed309"),
    "id" : 6,
    "num" : "006",
    "name" : "Charizard",
    "img" : "http://www.serebii.net/pokemongo/pokemon/006.png",
    "type" : [
        "Fire",
        "Flying"
    ],
    "height" : "1.70 m",
    "weight" : "90.5 kg",
    "candy" : "Charmander Candy",
    "egg" : "Not in Eggs",
    "spawn_chance" : 0.0031,
    "avg_spawns" : 0.31,
    "spawn_time" : "13:34",
    "multipliers" : null,
    "weaknesses" : [
        "Water",
        "Electric",
        "Rock"
    ],
    "prev_evolution" : [
        {
            "num" : "004",
            "name" : "Charmander"
        },
        {
            "num" : "005",
            "name" : "Charmeleon"
        }
    ]
}
```

**Salida adicional:**
```
Type "it" for more
```

**📖 Explicación:**  
- El método `.pretty()` formatea la salida JSON para que sea más legible
- MongoDB muestra solo **20 documentos por lote** por defecto
- Observa la estructura de documento: campos simples (`name`, `height`), arrays (`type`, `weaknesses`) y documentos embebidos (`next_evolution`)
- El campo `_id` es generado automáticamente por MongoDB usando `ObjectId` (12 bytes: timestamp + machine id + process id + counter)

**🔗 Concepto de Clase 3:**  
Esta es la característica principal de los **Document Stores**: estructura flexible donde cada documento puede tener campos diferentes. Bulbasaur tiene `next_evolution`, pero Charizard tiene `prev_evolution`.

---

### 1.4 Paginación con Cursores

Para ver más documentos, escribimos:

```javascript
it
```

**Salida esperada:**
```
20 documentos adicionales (Pokemon #21-40)
```

**📖 Explicación - Concepto de Cursores:**

Las consultas en MongoDB NO retornan documentos directamente, sino un **cursor**. Un cursor es un puntero que itera sobre los resultados.

**¿Por qué cursores?**
- **Performance**: Si tienes 10 millones de documentos, no quieres cargarlos todos en memoria
- **Eficiencia de red**: Solo transferimos los datos que necesitamos
- **Control de recursos**: El servidor MongoDB no se sobrecarga

**Tamaños de lote típicos:**
- MongoDB Shell: 20 documentos
- Drivers de programación: 101 documentos (configurable)

**🔗 Concepto de Clase 3:**  
Esto se relaciona con los **patrones de acceso** que discutimos. En sistemas distribuidos, minimizar la transferencia de datos es crítico para el rendimiento.

---

## 🔍 Parte 2: Queries y Filtros

### 2.1 Filtrado Básico por Campo Único

Buscamos un Pokemon específico por nombre:

```javascript
db.pokemon.find({ name: "Charizard" }).pretty()
```

**Salida esperada:**
```javascript
{
    "_id" : ObjectId("5a8744754c24cb5c863ed309"),
    "id" : 6,
    "num" : "006",
    "name" : "Charizard",
    "img" : "http://www.serebii.net/pokemongo/pokemon/006.png",
    "type" : [
        "Fire",
        "Flying"
    ],
    "height" : "1.70 m",
    "weight" : "90.5 kg",
    "candy" : "Charmander Candy",
    "egg" : "Not in Eggs",
    "spawn_chance" : 0.0031,
    "avg_spawns" : 0.31,
    "spawn_time" : "13:34",
    "multipliers" : null,
    "weaknesses" : [
        "Water",
        "Electric",
        "Rock"
    ],
    "prev_evolution" : [
        {
            "num" : "004",
            "name" : "Charmander"
        },
        {
            "num" : "005",
            "name" : "Charmeleon"
        }
    ]
}
```

**📖 Explicación:**  
- Pasamos un **documento de filtro** `{ name: "Charizard" }` al método `find()`
- MongoDB busca todos los documentos donde el campo `name` sea exactamente `"Charizard"`
- La búsqueda es **case-sensitive**: `"charizard"` no coincidiría

---

### 2.2 Filtrado por Múltiples Documentos

Buscamos todos los Pokemon de una altura específica:

```javascript
db.pokemon.find({ height: "1.70 m" }).pretty()
```

**Salida esperada (6 documentos):**
```javascript
// Documento 1: Charizard
{
    "_id" : ObjectId("5a8744754c24cb5c863ed309"),
    "name" : "Charizard",
    "height" : "1.70 m",
    "weight" : "90.5 kg",
    ...
}

// Documento 2: Golduck
{
    "_id" : ObjectId("5a8744754c24cb5c863ed33a"),
    "name" : "Golduck",
    "height" : "1.70 m",
    "weight" : "76.6 kg",
    ...
}

// Documento 3: Victreebel
{
    "_id" : ObjectId("5a8744754c24cb5c863ed34a"),
    "name" : "Victreebel",
    "height" : "1.70 m",
    "weight" : "15.5 kg",
    ...
}

// Documento 4: Rapidash
{
    "_id" : ObjectId("5a8744754c24cb5c863ed351"),
    "name" : "Rapidash",
    "height" : "1.70 m",
    "weight" : "95.0 kg",
    ...
}

// Documento 5: Dewgong
{
    "_id" : ObjectId("5a8744754c24cb5c863ed35a"),
    "name" : "Dewgong",
    "height" : "1.70 m",
    "weight" : "120.0 kg",
    ...
}

// Documento 6: Articuno
{
    "_id" : ObjectId("5a8744754c24cb5c863ed393"),
    "name" : "Articuno",
    "height" : "1.70 m",
    "weight" : "55.4 kg",
    ...
}
```

**📖 Explicación:**  
Esta consulta encontró **6 Pokemon** que miden exactamente 1.70 metros: Charizard, Golduck, Victreebel, Rapidash, Dewgong y Articuno.

---

### 2.3 Dot Notation para Campos Anidados

MongoDB permite acceder a campos dentro de documentos embebidos usando **dot notation**:

```javascript
db.pokemon.find({ "next_evolution.name": "Venusaur" }).pretty()
```

**Salida esperada:**
```javascript
{
    "_id" : ObjectId("5a8744754c24cb5c863ed308"),
    "name" : "Bulbasaur",
    "type" : ["Grass", "Poison"],
    "next_evolution" : [
        {
            "num" : "002",
            "name" : "Ivysaur"
        },
        {
            "num" : "003",
            "name" : "Venusaur"
        }
    ],
    ...
}
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30a"),
    "name" : "Ivysaur",
    "type" : ["Grass", "Poison"],
    "next_evolution" : [
        {
            "num" : "003",
            "name" : "Venusaur"
        }
    ],
    ...
}
```

**📖 Explicación:**  
- Usamos `"next_evolution.name"` (con comillas) para acceder al campo `name` dentro del array `next_evolution`
- Esto encuentra Pokemon que evolucionan a Venusaur (Bulbasaur e Ivysaur)
- La notación de punto funciona para documentos embebidos y arrays

**🔗 Concepto de Clase 3:**  
Esta es la ventaja del **embedding**: podemos consultar información de evoluciones sin hacer JOINs. En un modelo relacional, necesitaríamos una tabla separada `evolutions` y un JOIN costoso.

📖 **Más información:** [Dot Notation en MongoDB](https://docs.mongodb.com/manual/core/document/#dot-notation)

---

## 🔢 Parte 3: Operadores de Comparación

MongoDB ofrece operadores especiales que empiezan con `$` para consultas avanzadas.

### 3.1 Operador $gt (Greater Than - Mayor que)

Buscamos Pokemon con peso mayor a 90 kg:

```javascript
db.pokemon.find({ weight: { $gt: "90.0 kg" } }).pretty()
```

**Salida esperada (2 documentos):**
```javascript
// Documento 1: Charizard
{
    "_id" : ObjectId("5a8744754c24cb5c863ed309"),
    "name" : "Charizard",
    "weight" : "90.5 kg",
    "height" : "1.70 m",
    ...
}

// Documento 2: Rapidash
{
    "_id" : ObjectId("5a8744754c24cb5c863ed351"),
    "name" : "Rapidash",
    "weight" : "95.0 kg",
    "height" : "1.70 m",
    ...
}
```

**📖 Explicación:**  
- El operador `$gt` (greater than) compara valores
- Solo 2 Pokemon pesan más de 90 kg: Charizard (90.5 kg) y Rapidash (95.0 kg)
- **Nota**: La comparación de strings puede ser problemática; en producción, almacenarías el peso como número

**Operadores de comparación disponibles:**
- `$gt`: Mayor que (>)
- `$gte`: Mayor o igual que (≥)
- `$lt`: Menor que (<)
- `$lte`: Menor o igual que (≤)
- `$eq`: Igual a (=)
- `$ne`: Diferente de (≠)

---

### 3.2 Operador $lt (Less Than - Menor que)

Buscamos los Pokemon más raros (spawn_chance < 0.1%):

```javascript
db.pokemon.find({ spawn_chance: { $lt: 0.001 } }).pretty()
```

**Salida esperada (6 documentos):**
```javascript
// Documento 1: Ditto
{
    "_id" : ObjectId("5a8744754c24cb5c863ed389"),
    "name" : "Ditto",
    "spawn_chance" : 0,
    ...
}

// Documento 2: Articuno
{
    "_id" : ObjectId("5a8744754c24cb5c863ed393"),
    "name" : "Articuno",
    "spawn_chance" : 0,
    ...
}

// Documento 3: Zapdos
{
    "_id" : ObjectId("5a8744754c24cb5c863ed394"),
    "name" : "Zapdos",
    "spawn_chance" : 0,
    ...
}

// Documento 4: Moltres
{
    "_id" : ObjectId("5a8744754c24cb5c863ed395"),
    "name" : "Moltres",
    "spawn_chance" : 0,
    ...
}

// Documento 5: Mewtwo
{
    "_id" : ObjectId("5a8744754c24cb5c863ed399"),
    "name" : "Mewtwo",
    "spawn_chance" : 0,
    ...
}

// Documento 6: Mew
{
    "_id" : ObjectId("5a8744754c24cb5c863ed39a"),
    "name" : "Mew",
    "spawn_chance" : 0,
    ...
}
```

**📖 Explicación:**  
Encontramos los **6 Pokemon más raros** del juego:
- **Legendarios**: Articuno, Zapdos, Moltres, Mewtwo, Mew
- **Especial**: Ditto

Todos tienen `spawn_chance: 0`, lo que significa que no aparecen de forma natural en el juego.

---

## 📊 Parte 4: Operadores sobre Arrays

### 4.1 Operador $size (Tamaño de Array)

Buscamos Pokemon con solo un tipo:

```javascript
db.pokemon.find({ type: { $size: 1 } }).pretty()
```

**Salida esperada (primeros 3 documentos):**
```javascript
// Documento 1: Charmander
{
    "_id" : ObjectId("5a8744754c24cb5c863ed307"),
    "name" : "Charmander",
    "type" : ["Fire"],
    ...
}

// Documento 2: Squirtle
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30b"),
    "name" : "Squirtle",
    "type" : ["Water"],
    ...
}

// Documento 3: Caterpie
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30e"),
    "name" : "Caterpie",
    "type" : ["Bug"],
    ...
}

// ... (muchos más)
```

**📖 Explicación:**  
- El operador `$size` verifica el tamaño exacto de un array
- Muchos Pokemon tienen solo un tipo (Fire, Water, Bug, Normal, etc.)
- Pokemon como Bulbasaur (Grass/Poison) o Charizard (Fire/Flying) tienen 2 tipos y NO aparecen en este resultado

**🔗 Concepto de Clase:**  
En un **modelo relacional**, necesitarías una tabla `pokemon_types` con relación muchos-a-muchos. En **Document Stores**, simplemente guardamos un array de tipos directamente en el documento.

---

### 4.2 Búsqueda en Arrays

MongoDB puede buscar valores dentro de arrays directamente:

```javascript
db.pokemon.find({ type: "Dragon" }).pretty()
```

**Salida esperada (3 documentos):**
```javascript
// Documento 1: Dratini
{
    "_id" : ObjectId("5a8744754c24cb5c863ed395"),
    "name" : "Dratini",
    "type" : ["Dragon"],
    ...
}

// Documento 2: Dragonair
{
    "_id" : ObjectId("5a8744754c24cb5c863ed396"),
    "name" : "Dragonair",
    "type" : ["Dragon"],
    ...
}

// Documento 3: Dragonite
{
    "_id" : ObjectId("5a8744754c24cb5c863ed397"),
    "name" : "Dragonite",
    "type" : ["Dragon", "Flying"],
    ...
}
```

**📖 Explicación:**  
- Cuando buscamos `{ type: "Dragon" }`, MongoDB verifica si "Dragon" está presente en el array `type`
- Encuentra Pokemon que tienen "Dragon" como único tipo (Dratini, Dragonair) o como uno de sus tipos (Dragonite)

---

## 🔍 Parte 5: Operadores de Existencia

### 5.1 Operador $exists

Buscamos Pokemon en su **evolución final** (sin campo `next_evolution`):

```javascript
db.pokemon.find({ next_evolution: { $exists: false } }).count()
```

**Salida esperada:**
```
78
```

Veamos algunos ejemplos:

```javascript
db.pokemon.find({ next_evolution: { $exists: false } }).limit(5).pretty()
```

**Salida esperada:**
```javascript
// Documento 1: Venusaur
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30a"),
    "name" : "Venusaur",
    "type" : ["Grass", "Poison"],
    "prev_evolution" : [
        {
            "num" : "001",
            "name" : "Bulbasaur"
        },
        {
            "num" : "002",
            "name" : "Ivysaur"
        }
    ]
    // NO tiene campo next_evolution
}

// Documento 2: Charizard
{
    "_id" : ObjectId("5a8744754c24cb5c863ed309"),
    "name" : "Charizard",
    "type" : ["Fire", "Flying"],
    "prev_evolution" : [
        {
            "num" : "004",
            "name" : "Charmander"
        },
        {
            "num" : "005",
            "name" : "Charmeleon"
        }
    ]
    // NO tiene campo next_evolution
}

// ... (3 más)
```

**📖 Explicación:**  
- El operador `$exists: false` encuentra documentos que **NO tienen** el campo especificado
- 78 de los 151 Pokemon están en su evolución final
- Estos Pokemon tienen `prev_evolution` (evoluciones anteriores) pero NO `next_evolution`

**🔗 Concepto de Clase 3:**  
Esta es la **flexibilidad del esquema** en Document Stores. No todos los documentos necesitan tener los mismos campos. En SQL, tendrías columnas `NULL` o tablas adicionales.

---

### 5.2 Combinando $exists con otros operadores

Buscamos Pokemon que tienen evoluciones previas Y spawn_chance mayor a 1%:

```javascript
db.pokemon.find({ 
    prev_evolution: { $exists: true },
    spawn_chance: { $gt: 0.01 }
}).pretty()
```

**Salida esperada (primeros 2 documentos):**
```javascript
// Documento 1: Ivysaur
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30a"),
    "name" : "Ivysaur",
    "spawn_chance" : 0.042,
    "prev_evolution" : [
        {
            "num" : "001",
            "name" : "Bulbasaur"
        }
    ],
    ...
}

// Documento 2: Wartortle
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30c"),
    "name" : "Wartortle",
    "spawn_chance" : 0.034,
    "prev_evolution" : [
        {
            "num" : "007",
            "name" : "Squirtle"
        }
    ],
    ...
}
```

**📖 Explicación:**  
Encontramos Pokemon evolucionados (tienen `prev_evolution`) que además son relativamente comunes (spawn_chance > 1%).

---

## 🔗 Parte 6: Operadores Lógicos

### 6.1 Operador $and (Y lógico)

Buscamos Pokemon que sean de tipo Water **Y** menores a 1.00 m:

```javascript
db.pokemon.find({ 
    $and: [ 
        { type: "Water" }, 
        { height: { $lt: "1.00 m" } } 
    ] 
}).pretty()
```

**Salida esperada (primeros 3 documentos):**
```javascript
// Documento 1: Squirtle
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30b"),
    "name" : "Squirtle",
    "type" : ["Water"],
    "height" : "0.51 m",
    ...
}

// Documento 2: Psyduck
{
    "_id" : ObjectId("5a8744754c24cb5c863ed339"),
    "name" : "Psyduck",
    "type" : ["Water"],
    "height" : "0.79 m",
    ...
}

// Documento 3: Poliwag
{
    "_id" : ObjectId("5a8744754c24cb5c863ed33d"),
    "name" : "Poliwag",
    "type" : ["Water"],
    "height" : "0.61 m",
    ...
}

// ... (más documentos)
```

**📖 Explicación:**  
- El operador `$and` recibe un array de condiciones
- Retorna documentos que cumplan **TODAS** las condiciones
- En este caso: tipo Water Y altura menor a 1.00 m

**Sintaxis simplificada:**  
En realidad, MongoDB asume `$and` implícitamente cuando pasas múltiples campos:

```javascript
// Estas dos consultas son equivalentes:
db.pokemon.find({ type: "Water", height: { $lt: "1.00 m" } })
db.pokemon.find({ $and: [ { type: "Water" }, { height: { $lt: "1.00 m" } } ] })
```

---

### 6.2 Operador $or (O lógico)

Buscamos Pokemon que sean tipo Dragon **O** tengan spawn_chance de 0:

```javascript
db.pokemon.find({ 
    $or: [ 
        { type: "Dragon" }, 
        { spawn_chance: 0 } 
    ] 
}).pretty()
```

**Salida esperada (9 documentos):**
```javascript
// Documento 1: Dratini (Dragon)
{
    "_id" : ObjectId("5a8744754c24cb5c863ed395"),
    "name" : "Dratini",
    "type" : ["Dragon"],
    "spawn_chance" : 0.3,
    ...
}

// Documento 2: Dragonair (Dragon)
{
    "_id" : ObjectId("5a8744754c24cb5c863ed396"),
    "name" : "Dragonair",
    "type" : ["Dragon"],
    ...
}

// Documento 3: Dragonite (Dragon)
{
    "_id" : ObjectId("5a8744754c24cb5c863ed397"),
    "name" : "Dragonite",
    "type" : ["Dragon", "Flying"],
    ...
}

// Documento 4: Ditto (spawn_chance = 0)
{
    "_id" : ObjectId("5a8744754c24cb5c863ed389"),
    "name" : "Ditto",
    "spawn_chance" : 0,
    ...
}

// Documento 5: Articuno (spawn_chance = 0)
{
    "_id" : ObjectId("5a8744754c24cb5c863ed393"),
    "name" : "Articuno",
    "spawn_chance" : 0,
    ...
}

// ... (4 legendarios más con spawn_chance = 0)
```

**📖 Explicación:**  
- El operador `$or` retorna documentos que cumplan **AL MENOS UNA** de las condiciones
- Encontramos 3 Pokemon Dragon + 6 legendarios con spawn_chance = 0 = 9 documentos totales

---

### 6.3 Combinando $and y $or

Consulta compleja: Pokemon que sean (Fire O Water) Y tengan spawn_chance > 5%:

```javascript
db.pokemon.find({ 
    $and: [
        { 
            $or: [ 
                { type: "Fire" }, 
                { type: "Water" } 
            ] 
        },
        { spawn_chance: { $gt: 0.05 } }
    ]
}).pretty()
```

**Salida esperada (primeros 3 documentos):**
```javascript
// Documento 1: Charmander
{
    "_id" : ObjectId("5a8744754c24cb5c863ed307"),
    "name" : "Charmander",
    "type" : ["Fire"],
    "spawn_chance" : 0.253,
    ...
}

// Documento 2: Squirtle
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30b"),
    "name" : "Squirtle",
    "type" : ["Water"],
    "spawn_chance" : 0.58,
    ...
}

// Documento 3: Wartortle
{
    "_id" : ObjectId("5a8744754c24cb5c863ed30c"),
    "name" : "Wartortle",
    "type" : ["Water"],
    "spawn_chance" : 0.034,
    ...
}

// ... (más documentos)
```

**📖 Explicación:**  
Esta consulta tiene lógica compleja:
1. **OR interno**: tipo Fire O tipo Water
2. **AND externo**: Y que además tenga spawn_chance > 5%

Esto encuentra Pokemon de fuego o agua que sean relativamente comunes en el juego.

📖 **Referencia completa:** [Query and Projection Operators](https://docs.mongodb.com/manual/reference/operator/query/)

---

## 📋 Parte 7: Proyección (Selección de Campos)

### 7.1 Proyección Básica

Si no queremos todos los campos, usamos un **documento de proyección** como segundo argumento:

```javascript
db.pokemon.find(
    { height: "1.70 m" }, 
    { name: true, weight: true }
).pretty()
```

**Salida esperada:**
```javascript
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

**📖 Explicación:**  
- Primer argumento: filtro `{ height: "1.70 m" }`
- Segundo argumento: proyección `{ name: true, weight: true }`
- Solo retorna los campos `name` y `weight` (más `_id` por defecto)
- El resto de campos (type, height, spawn_chance, etc.) NO se transfieren por la red

**🔗 Concepto de Clase 3:**  
La proyección es crítica en **sistemas distribuidos** para:
- **Reducir ancho de banda**: No transferimos campos innecesarios
- **Mejorar latencia**: Menos datos = respuesta más rápida
- **Optimizar memoria**: Menos carga en el cliente

---

### 7.2 Excluir el Campo _id

El campo `_id` siempre se muestra por defecto. Para ocultarlo:

```javascript
db.pokemon.find(
    { height: "1.70 m" }, 
    { _id: false, name: true, weight: true }
).pretty()
```

**Salida esperada:**
```javascript
{ "name" : "Charizard", "weight" : "90.5 kg" }
{ "name" : "Golduck", "weight" : "76.6 kg" }
{ "name" : "Victreebel", "weight" : "15.5 kg" }
{ "name" : "Rapidash", "weight" : "95.0 kg" }
{ "name" : "Dewgong", "weight" : "120.0 kg" }
{ "name" : "Articuno", "weight" : "55.4 kg" }
```

**📖 Explicación:**  
Ahora la salida es mucho más limpia, mostrando solo los campos que pedimos explícitamente.

---

### 7.3 Proyección por Exclusión

También podemos excluir campos específicos en lugar de incluirlos:

```javascript
db.pokemon.find(
    { name: "Pikachu" }, 
    { img: false, weaknesses: false, multipliers: false }
).pretty()
```

**Salida esperada:**
```javascript
{
    "_id" : ObjectId("5a8744754c24cb5c863ed31c"),
    "id" : 25,
    "num" : "025",
    "name" : "Pikachu",
    "type" : ["Electric"],
    "height" : "0.41 m",
    "weight" : "6.0 kg",
    "candy" : "Pikachu Candy",
    "candy_count" : 50,
    "egg" : "2 km",
    "spawn_chance" : 0.21,
    "avg_spawns" : 21,
    "spawn_time" : "04:00",
    "next_evolution" : [
        {
            "num" : "026",
            "name" : "Raichu"
        }
    ]
    // NO aparecen: img, weaknesses, multipliers
}
```

**📖 Explicación:**  
- Usamos `false` para excluir campos específicos
- Todos los demás campos se incluyen automáticamente
- **⚠️ Nota**: No puedes mezclar inclusión (`true`) y exclusión (`false`) excepto para `_id`

---

## ✏️ Parte 8: Actualización de Documentos

### 8.1 Actualizar un Solo Documento

Primero, veamos el documento actual de Psyduck:

```javascript
db.pokemon.find(
    { name: "Psyduck" }, 
).pretty()
```

**Salida esperada:**
```javascript
{
    "_id" : ObjectId("5a8744754c24cb5c863ed339"),
    "name" : "Psyduck",
    "height" : "0.79 m",
    "weight" : "19.6 kg",
    ...
}
```

Ahora **modificamos la altura** de Psyduck usando `updateOne`:

```javascript
db.pokemon.updateOne(
    { name: "Psyduck" }, 
    { $set: { height: "10.0 m" } }
)
```

**Salida esperada:**
```javascript
{ 
    "acknowledged" : true, 
    "matchedCount" : 1, 
    "modifiedCount" : 1 
}
```

**📖 Explicación de la salida:**
- `acknowledged: true` → La operación fue reconocida por el servidor
- `matchedCount: 1` → Se encontró 1 documento que coincide con el filtro
- `modifiedCount: 1` → Se modificó 1 documento

Verificamos el cambio:

```javascript
db.pokemon.find(
    { name: "Psyduck" }, 
    { name: true, height: true, weight: true }
).pretty()
```

**Salida esperada:**
```javascript
{
    "_id" : ObjectId("5a8744754c24cb5c863ed339"),
    "name" : "Psyduck",
    "height" : "10.0 m",  // ← CAMBIÓ de "0.79 m" a "10.0 m"
    "weight" : "19.6 kg"
}
```

**📖 Explicación del operador $set:**
- `$set` establece el valor de un campo
- Si el campo no existe, lo crea
- Si el campo existe, lo reemplaza

---

### 9.2 Actualizar Múltiples Documentos

Incrementemos `avg_spawns` en 1 para los Pokemon más pesados:

```javascript
db.pokemon.updateMany(
    { weight: { $gt: "90.0 kg" } }, 
    { $inc: { avg_spawns: 1 } }
)
```

**Salida esperada:**
```javascript
{ 
    "acknowledged" : true, 
    "matchedCount" : 2, 
    "modifiedCount" : 2 
}
```

Verificamos los cambios:

```javascript
db.pokemon.find(
    { weight: { $gt: "90.0 kg" } },
    { name: true, weight: true, avg_spawns: true }
).pretty()
```

**Salida esperada:**
```javascript
// Antes: avg_spawns era 0.31
{
    "_id" : ObjectId("5a8744754c24cb5c863ed309"),
    "name" : "Charizard",
    "weight" : "90.5 kg",
    "avg_spawns" : 1.31  // ← Incrementado en 1
}

// Antes: avg_spawns era 4
{
    "_id" : ObjectId("5a8744754c24cb5c863ed351"),
    "name" : "Rapidash",
    "weight" : "95.0 kg",
    "avg_spawns" : 5  // ← Incrementado en 1
}
```

**📖 Explicación del operador $inc:**
- `$inc` incrementa (o decrementa si es negativo) un valor numérico
- `{ $inc: { avg_spawns: 1 } }` suma 1 al campo `avg_spawns`
- `{ $inc: { avg_spawns: -1 } }` restaría 1

---

**📖 Operadores de actualización disponibles:**

| Operador | Descripción | Ejemplo |
|----------|-------------|---------|
| `$set` | Establece el valor de un campo | `{ $set: { height: "1.5 m" } }` |
| `$unset` | Elimina un campo | `{ $unset: { multipliers: "" } }` |
| `$inc` | Incrementa un valor numérico | `{ $inc: { avg_spawns: 1 } }` |
| `$push` | Agrega elemento a un array | `{ $push: { type: "Dragon" } }` |
| `$pull` | Elimina elemento de un array | `{ $pull: { type: "Poison" } }` |
| `$addToSet` | Agrega elemento solo si no existe | `{ $addToSet: { type: "Fire" } }` |
| `$pop` | Elimina el primer/último elemento | `{ $pop: { weaknesses: 1 } }` |
| `$rename` | Renombra un campo | `{ $rename: { "name": "pokemon_name" } }` |

📖 **Referencia completa:** [Update Operators](https://docs.mongodb.com/manual/reference/operator/update/)



## 🚀 Parte 9: Índices y Rendimiento

### 9.1 Consultas Sin Índice (Scan Completo)

Primero, analicemos cómo MongoDB ejecuta una consulta sin índice:

```javascript
db.pokemon.find({ name: "Mew" }).explain("executionStats")
```

**Salida esperada (simplificada):**
```javascript
{
    "executionStats" : {
        "executionSuccess" : true,
        "nReturned" : 1,              // Retornó 1 documento
        "executionTimeMillis" : 3,    // Tomó 3 milisegundos
        "totalKeysExamined" : 0,      // No usó índices
        "totalDocsExamined" : 150,    // ⚠️ Examinó 150 documentos!
        "executionStages" : {
            "stage" : "COLLSCAN",     // ⚠️ Collection Scan (malo)
            ...
        }
    },
    ...
}
```

**📖 Explicación del Problema:**
- **`totalDocsExamined: 150`** → MongoDB tuvo que leer **TODOS** los documentos de la colección
- **`stage: "COLLSCAN"`** → Hizo un "Collection Scan" (escaneo completo)
- **Complejidad:** O(n) - Linear time
- **Problema:** Si tienes 1,000,000 de documentos, MongoDB examinaría 1,000,000 documentos para encontrar 1 resultado

**🔗 Concepto de Clase 3:**
Esto es extremadamente ineficiente. Es como buscar un libro en una biblioteca revisando **TODOS** los estantes en orden, en lugar de usar el catálogo.

---

### 11.2 Crear un Índice

Los **índices** son estructuras de datos (B-Trees en MongoDB) que permiten búsquedas rápidas. Creemos un índice en el campo `name`:

```javascript
db.pokemon.createIndex({ name: 1 })
```

**Salida esperada:**
```javascript
{
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,    // Ya existía 1 índice (_id)
    "numIndexesAfter" : 2,     // Ahora hay 2 índices
    "ok" : 1
}
```

**📖 Explicación:**
- `{ name: 1 }` crea un índice en el campo `name` en orden **ascendente**
- `1` = ascendente (A-Z)
- `-1` = descendente (Z-A)
- MongoDB ya tiene un índice automático en `_id`

**🔗 Concepto de Clase 3:**
Los índices en MongoDB usan **B-Trees** (que vimos en clase). Un B-Tree permite búsquedas en O(log n) en lugar de O(n).

---

### 11.3 Consultas Con Índice (Búsqueda Optimizada)

Ahora ejecutemos la misma consulta:

```javascript
db.pokemon.find({ name: "Mew" }).explain("executionStats")
```

**Salida esperada (simplificada):**
```javascript
{
    "executionStats" : {
        "executionSuccess" : true,
        "nReturned" : 1,              // Retornó 1 documento
        "executionTimeMillis" : 0,    // ⚡ Instantáneo!
        "totalKeysExamined" : 1,      // ✅ Usó 1 entrada del índice
        "totalDocsExamined" : 1,      // ✅ Solo examinó 1 documento!
        "executionStages" : {
            "stage" : "FETCH",        // ✅ Usó índice (bueno)
            "inputStage" : {
                "stage" : "IXSCAN",   // ✅ Index Scan
                "keyPattern" : {
                    "name" : 1
                },
                ...
            }
        }
    },
    ...
}
```

**📖 Análisis de la Mejora:**

| Métrica | Sin Índice | Con Índice | Mejora |
|---------|-----------|-----------|--------|
| **totalDocsExamined** | 150 | 1 | **150x más eficiente** |
| **executionTimeMillis** | 3 ms | 0 ms | **Instantáneo** |
| **Tipo de búsqueda** | COLLSCAN | IXSCAN | **Óptimo** |

**🔗 Concepto de Clase 3:**

### **Complejidad Computacional:**
- **Sin índice:** O(n) - debe revisar todos los documentos
- **Con índice:** O(log n) - usa B-Tree para búsqueda binaria

### **Ejemplo con 1,000,000 de documentos:**
- Sin índice: ~1,000,000 operaciones
- Con índice: ~20 operaciones (log₂ 1,000,000 ≈ 20)

**¡Es 50,000x más rápido!** 🚀

---

### 9.4 Índices Compuestos

Podemos crear índices en **múltiples campos**:

```javascript
db.pokemon.createIndex({ type: 1, spawn_chance: -1 })
```

**Salida esperada:**
```javascript
{
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 2,
    "numIndexesAfter" : 3,
    "ok" : 1
}
```

Este índice es útil para consultas como:

```javascript
db.pokemon.find({ type: "Water" }).sort({ spawn_chance: -1 }).explain("executionStats")
```

**📖 Explicación:**
- Índice compuesto en `type` (ascendente) y `spawn_chance` (descendente)
- Optimiza consultas que filtran por tipo Y ordenan por spawn_chance
- El orden importa: `{ type: 1, spawn_chance: -1 }` ≠ `{ spawn_chance: -1, type: 1 }`

---

### 11.6 Trade-offs de los Índices

**✅ Ventajas:**
- **Lecturas rápidas:** O(log n) en lugar de O(n)
- **Ordenamiento eficiente:** `sort()` usa el índice
- **Búsquedas complejas:** Índices compuestos para queries multi-campo

**❌ Desventajas:**
- **Escrituras más lentas:** Cada insert/update/delete debe actualizar los índices
- **Espacio en disco:** Los índices ocupan almacenamiento adicional
- **Memoria:** Los índices se cargan en RAM para ser rápidos

**🔗 Concepto de Clase:**
Esto es un **trade-off clásico** en sistemas de bases de datos:
- **Read-heavy workloads** → Crear muchos índices (optimizar lecturas)
- **Write-heavy workloads** → Minimizar índices (optimizar escrituras)
- Relacionado con **LSM-Trees vs B-Trees** que vimos en clase

📖 **Profundiza:** [Documentación de Índices en MongoDB](https://docs.mongodb.com/manual/indexes/)

---

## 🔄 Parte 10: Aggregation Framework

El **Aggregation Framework** permite realizar operaciones complejas de transformación, agrupación y análisis de datos.

### 10.1 Seleccionar un Documento Aleatorio

```javascript
db.pokemon.aggregate([
    { $sample: { size: 1 } }
])
```

**Salida esperada (varía cada vez):**
```javascript
{
    "_id" : ObjectId("..."),
    "id" : 54,
    "name" : "Psyduck",
    "type" : ["Water"],
    ...
}
```

**📖 Explicación:**
- `$sample` selecciona documentos aleatorios de la colección
- `size: 1` significa "dame 1 documento aleatorio"
- Útil para obtener datos de prueba o muestras estadísticas

---

### 10.2 Agrupar y Contar por Tipo

Contemos cuántos Pokemon hay de cada tipo:

```javascript
db.pokemon.aggregate([
    { $unwind: "$type" },
    { $group: { 
        _id: "$type", 
        count: { $sum: 1 } 
    }},
    { $sort: { count: -1 } }
])
```

**Salida esperada:**
```javascript
{ "_id" : "Water", "count" : 32 }
{ "_id" : "Normal", "count" : 22 }
{ "_id" : "Poison", "count" : 28 }
{ "_id" : "Bug", "count" : 12 }
{ "_id" : "Grass", "count" : 12 }
{ "_id" : "Fire", "count" : 12 }
{ "_id" : "Psychic", "count" : 14 }
{ "_id" : "Flying", "count" : 19 }
{ "_id" : "Rock", "count" : 11 }
{ "_id" : "Ground", "count" : 10 }
{ "_id" : "Fighting", "count" : 8 }
{ "_id" : "Electric", "count" : 9 }
{ "_id" : "Ghost", "count" : 3 }
{ "_id" : "Ice", "count" : 5 }
{ "_id" : "Dragon", "count" : 3 }
{ "_id" : "Fairy", "count" : 5 }
{ "_id" : "Steel", "count" : 2 }
{ "_id" : "Dark", "count" : 0 }
```

**📖 Explicación del Pipeline:**

1. **`$unwind: "$type"`**
   - Descompone el array `type` en documentos individuales
   - Pikachu con `type: ["Electric"]` genera 1 documento
   - Charizard con `type: ["Fire", "Flying"]` genera 2 documentos

2. **`$group: { _id: "$type", count: { $sum: 1 } }`**
   - Agrupa documentos por tipo (`_id: "$type"`)
   - Cuenta cuántos documentos hay por grupo (`count: { $sum: 1 }`)

3. **`$sort: { count: -1 }`**
   - Ordena por conteo en orden descendente
   - El tipo más común aparece primero

**Resultado:** Water es el tipo más común (32 Pokemon), seguido de Poison (28) y Normal (22).

---

### 10.3 Calcular Promedios por Tipo

Calculemos el **peso promedio** de Pokemon por tipo:

```javascript
db.pokemon.aggregate([
    { $unwind: "$type" },
    { $group: { 
        _id: "$type",
        avgWeight: { 
            $avg: { 
                $toDouble: { 
                    $substr: ["$weight", 0, { $subtract: [{ $strLenCP: "$weight" }, 3] }] 
                } 
            } 
        },
        count: { $sum: 1 }
    }},
    { $sort: { avgWeight: -1 } },
    { $limit: 10 }
])
```

**Salida esperada:**
```javascript
{ "_id" : "Steel", "avgWeight" : 400, "count" : 2 }
{ "_id" : "Rock", "avgWeight" : 102.45, "count" : 11 }
{ "_id" : "Ground", "avgWeight" : 88.5, "count" : 10 }
{ "_id" : "Dragon", "avgWeight" : 90.33, "count" : 3 }
{ "_id" : "Fighting", "avgWeight" : 68.75, "count" : 8 }
{ "_id" : "Water", "avgWeight" : 56.34, "count" : 32 }
{ "_id" : "Ice", "avgWeight" : 54.2, "count" : 5 }
{ "_id" : "Normal", "avgWeight" : 52.36, "count" : 22 }
{ "_id" : "Poison", "avgWeight" : 48.5, "count" : 28 }
{ "_id" : "Fire", "avgWeight" : 45.92, "count" : 12 }
```

**📖 Explicación del Pipeline:**

1. **`$unwind: "$type"`** - Descompone el array de tipos

2. **`$group` con `$avg`:**
   - **`$substr`**: Extrae el número del string `"90.5 kg"` → `"90.5"`
   - **`$toDouble`**: Convierte el string a número → `90.5`
   - **`$avg`**: Calcula el promedio de los pesos

3. **`$sort: { avgWeight: -1 }`** - Ordena por peso promedio descendente

4. **`$limit: 10`** - Muestra solo los top 10

**Resultado:** Los Pokemon tipo Ice son los más pesados, seguidos por Rock y Ground.

---

### 10.4 Operadores de Aggregation Más Comunes

| Operador | Descripción | Ejemplo |
|----------|-------------|---------|
| **`$match`** | Filtra documentos (como `find()`) | `{ $match: { type: "Fire" } }` |
| **`$group`** | Agrupa documentos y calcula agregaciones | `{ $group: { _id: "$type", total: { $sum: 1 } } }` |
| **`$sort`** | Ordena documentos | `{ $sort: { spawn_chance: -1 } }` |
| **`$limit`** | Limita el número de documentos | `{ $limit: 10 }` |
| **`$skip`** | Salta documentos (paginación) | `{ $skip: 20 }` |
| **`$project`** | Selecciona/transforma campos | `{ $project: { name: 1, type: 1 } }` |
| **`$unwind`** | Descompone arrays | `{ $unwind: "$type" }` |
| **`$lookup`** | JOIN con otra colección | `{ $lookup: { from: "trainers", ... } }` |
| **`$sample`** | Selecciona documentos aleatorios | `{ $sample: { size: 5 } }` |
| **`$count`** | Cuenta documentos | `{ $count: "total" }` |

**🔗 Concepto de Clase:**
El Aggregation Framework es similar a **MapReduce** que vimos en clase:
- **Map** → `$unwind`, `$project` (transforman datos)
- **Reduce** → `$group` (agrega/combina datos)
- **Pipeline** → Múltiples etapas de procesamiento

📖 **Referencia completa:** [Aggregation Pipeline](https://docs.mongodb.com/manual/aggregation/)

---

## 🎓 Parte 11: Resumen y Reflexión


### 11.1 Comparación: MongoDB vs Modelo Relacional

| Aspecto | MongoDB (Document Store) | PostgreSQL (Relacional) |
|---------|-------------------------|------------------------|
| **Esquema** | Flexible, dinámico | Rígido, definido por schema |
| **Estructura** | Documentos JSON anidados | Tablas normalizadas |
| **Relaciones** | Embedding o referencias | Foreign keys + JOINs |
| **Consultas complejas** | Aggregation Pipeline | SQL con JOINs |
| **Escalabilidad** | Sharding horizontal nativo | Vertical, sharding complejo |
| **Consistencia** | Eventual (configurable) | ACID fuerte |
| **Uso ideal** | Datos semi-estructurados, alta escala | Datos estructurados, transacciones complejas |

**🔗 Concepto de Clase 3:**
Esta tabla refleja los **trade-offs** entre modelos que discutimos:
- **BASE vs ACID**: MongoDB prioriza disponibilidad y particionamiento (CAP)
- **Document vs Relational**: Embedding vs normalización

---

### 11.2 ¿Cuándo Usar MongoDB?

✅ **Casos de uso ideales:**
- **Catálogos de productos** (e-commerce): Productos con atributos variables
- **Content Management Systems**: Artículos, posts, páginas con estructura flexible
- **IoT y time-series**: Alta ingesta de datos semi-estructurados
- **Real-time analytics**: Aggregation Framework para análisis rápido
- **Mobile/gaming backends**: JSON nativo, fácil de integrar
- **Prototipado rápido**: Esquema flexible acelera el desarrollo

❌ **Cuándo NO usar MongoDB:**
- **Transacciones complejas multi-documento** (usar PostgreSQL)
- **Relaciones muy complejas con muchos JOINs** (usar SQL)
- **Reporting complejo con SQL** (usar data warehouses)
- **Datos altamente estructurados y estables** (usar SQL)
