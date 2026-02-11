# Introducción Moderna a MongoDB

MongoDB es una base de datos NoSQL orientada a documentos ampliamente utilizada por su flexibilidad, escalabilidad y facilidad de integración con aplicaciones modernas, especialmente Node.js.

## Contenido
- [¿Qué es MongoDB?](#qué-es-mongodb)
- [Bases de datos relacionales vs NoSQL](#bases-de-datos-relacionales-vs-nosql)
- [Modelo de documentos en MongoDB](#modelo-de-documentos-en-mongodb)
- [JSON y BSON](#json-y-bson)
- [Ejemplo comparativo: SQL vs MongoDB](#ejemplo-comparativo-sql-vs-mongodb)
- [Ventajas de MongoDB](#ventajas-de-mongodb)

## ¿Qué es MongoDB?
MongoDB es una base de datos NoSQL orientada a documentos. Almacena datos en documentos BSON (Binary JSON), lo que permite estructuras de datos flexibles y jerárquicas.

**Características clave:**
- Modelo de datos flexible y dinámico.
- Escalabilidad horizontal mediante sharding.
- Consultas avanzadas con MongoDB Query Language (MQL).
- Integración nativa con objetos de programación (especialmente JavaScript/Node.js).
- No requiere esquemas rígidos.

## Bases de datos relacionales vs NoSQL

### SQL (Relacional)
- Datos almacenados en tablas con filas y columnas.
- Uso de SQL para consultas.
- Requiere esquemas definidos de antemano.
- Necesidad de Object-Relational Mapping (ORM) para trabajar con objetos de programación.

**Ejemplo de tabla:**

**People**
| id | name  | age | location      |
|----|-------|-----|---------------|
| 1  | Ash   | 10  | Pallet Town   |
| 2  | Misty | 10  | Cerulean City |
| 3  | Brock | 15  | Pewter City   |

**Pokemon**
| id | name       | type     | owner_id |
|----|------------|----------|---------|
| 1  | Pikachu    | Electric | 1       |
| 2  | Charmander | Fire     | 1       |
| 3  | Staryu     | Water    | 2       |
| 4  | Starmie    | Water    | 2       |
| 5  | Onyx       | Rock     | 3       |

### NoSQL (MongoDB)
- Datos almacenados como documentos JSON/BSON.
- Esquema flexible.
- Se puede almacenar información anidada y listas internas.
- Evita la necesidad de ORM.

## Modelo de documentos en MongoDB
MongoDB organiza los datos en **colecciones**, que contienen **documentos**. Cada documento es equivalente a un objeto en programación.

**Ejemplo de documento de un entrenador Pokémon:**

```json
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
```

## JSON y BSON

- **JSON**: Formato ligero para representar datos estructurados. Compatible con la mayoría de lenguajes.
- **BSON**: Extensión binaria de JSON utilizada por MongoDB. Incluye más tipos de datos (fechas, enteros de distinto tamaño, binarios, etc.) y permite operaciones más eficientes.

## Ejemplo comparativo: SQL vs MongoDB

### SQL (Consultar Pokémon de Ash)
```sql
SELECT name, type
FROM pokemon
WHERE owner_id = 1;
```
**Resultado:**
| name       | type     |
|------------|----------|
| Pikachu    | Electric |
| Charmander | Fire     |

### MongoDB (Consultar Pokémon de Ash)
```javascript
db.trainers.find(
  { name: "Ash" },
  { _id: 0, pokemon: 1 }
)
```
**Resultado:**
```json
[
  {
    "pokemon": [
      { "id": 1, "name": "Pikachu", "type": "Electric" },
      { "id": 2, "name": "Charmander", "type": "Fire" }
    ]
  }
]
```

## Ventajas de MongoDB
- **Flexibilidad**: documentos anidados y arrays.
- **Escalabilidad**: sharding y clustering integrados.
- **Consultas expresivas**: filtros, agregaciones y operaciones complejas.
- **Integración natural**: objetos en Node.js o cualquier lenguaje moderno.
- **Menos complejidad**: evita ORM y migraciones complejas.

---

Este documento sirve como introducción moderna y técnica a MongoDB, preparado para desarrolladores y arquitectos de software que buscan adoptar bases de datos NoSQL en proyectos reales.

