# 🗄️ Importar Base de Datos en MongoDB Atlas

Este documento explica cómo crear una base de datos y **importar documentos JSON** directamente en MongoDB Atlas usando el **Data Explorer**.

---

## 1️⃣ Abrir Data Explorer

1. Ve a tu cluster en **MongoDB Atlas**.
2. Haz clic en **Connect** → **Data Explorer**.
3. Se abrirá el **Data Explorer**, donde podrás explorar y modificar tus bases de datos.

> 💡 Tip: Data Explorer permite crear colecciones y documentos sin usar la terminal.

---

## 2️⃣ Crear una base de datos

1. Haz clic en **Create Database**.
2. Completa los campos:

| Campo                 | Valor Ejemplo       |
|-----------------------|------------------|
| **DB Name**           | `pokedex`        |
| **Collection Name**   | `pokemon`        |
| **Additional Settings** | Opcional         |

3. Haz clic en **Create Database** ✅

> ⚠️ Nota: Una vez creada la base de datos, no se puede cambiar el nombre del DB ni de la colección.

---

## 3️⃣ Insertar o importar documentos

1. Dentro de la colección `pokemon`, haz clic en **Insert Document**.
2. Selecciona **Insert JSON** o **Overwrite with JSON**.
3. Pega tu contenido JSON dentro del editor:

[Archivo JSON](pokedex.json)
