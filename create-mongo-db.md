# Crear un Cluster MongoDB en la Nube

Este documento explica cómo crear un **cluster MongoDB gratuito en la nube** usando **MongoDB Atlas**, sin recurrir a terceros.

---

## 1. Crear una cuenta en MongoDB Atlas

1. Ve a [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).
2. Haz clic en **Start Free** o **Sign Up**.
3. Regístrate con tu correo o con tu cuenta de Google/GitHub.
4. Confirma tu correo si es necesario.

---

## 2. Crear un proyecto

1. Una vez dentro de Atlas, haz clic en **New Project**.
2. Asigna un nombre a tu proyecto, por ejemplo `ProyectoPrueba`.
3. Puedes agregar miembros si deseas colaboración (opcional).
4. Haz clic en **Create Project**.

---

## 3. Crear un cluster gratuito

1. Dentro de tu proyecto, haz clic en **Build a Cluster**.
2. Selecciona la opción **Shared Cluster (Free Tier)**.
3. Elige un proveedor de nube:
   - AWS, Google Cloud o Azure.
4. Selecciona la región más cercana a ti (ej. `US East` o `South America` si está disponible).
5. Haz clic en **Create Cluster**.
   - Este proceso puede tardar unos minutos.

---

## 4. Configurar el acceso

### 4.1 Crear un usuario de base de datos

1. Ve a **Database Access** > **Add New Database User**.
2. Asigna un usuario y contraseña.
3. Selecciona los permisos `Read and write to any database`.
4. Haz clic en **Add User**.

### 4.2 Permitir conexiones desde tu IP

1. Ve a **Network Access** > **Add IP Address**.
2. Selecciona **Allow Access from Anywhere** (0.0.0.0/0) o agrega tu IP específica.
3. Haz clic en **Confirm**.

---

## 5. Conectarse al cluster

1. Ve a **Clusters** > **Connect**.
2. Selecciona **Connect with Mongo Shell**.
3. Si no tienes Mongo Shell, descárgalo desde [MongoDB Shell](https://www.mongodb.com/try/download/shell) y sigue la instalación según tu SO.

### 5.1 Verificar instalación de mongosh

```bash
mongosh --version
```

### 5.2 Conectarse al cluster

1. Copia el string de conexión que te da Atlas (reemplaza `<password>` por la contraseña de tu usuario):

```bash
mongosh "mongodb+srv://<tu-usuario>:<password>@<tu-cluster>.mongodb.net/test"
```
2. Presiona Enter y ya estarás conectado a tu cluster gratuito en la nube.

## 6. Crear base de datos y colecciones

Una vez dentro de mongosh, puedes ejecutar comandos como:

```bash
use miBaseDatos
db.createCollection("miColeccion")
db.miColeccion.insertOne({nombre: "Juan", rol: "Arquitecto de Software"})
```

Esto crea una base de datos y una colección con un documento de ejemplo.
