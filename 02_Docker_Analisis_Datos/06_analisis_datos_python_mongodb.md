# Análisis de Datos con Python y MongoDB
## Introducción

### ¿Por qué Python y MongoDB?

En el panorama actual del análisis de datos, la combinación de Python y MongoDB representa una de las opciones más potentes y flexibles para trabajar con grandes volúmenes de datos no estructurados o semiestructurados. Esta combinación aborda dos aspectos fundamentales del análisis moderno: procesamiento/análisis y almacenamiento/gestión de datos no relacionales.

MongoDB se ha convertido en la base de datos NoSQL líder mundial por razones muy sólidas:

- Ofrece un modelo de datos flexible basado en documentos JSON (BSON), ideal para datos que no encajan en estructuras tabulares rígidas
- Proporciona escalabilidad horizontal nativa para manejar desde pequeños conjuntos hasta terabytes de información
- Permite consultas ricas y expresivas sobre estructuras de datos complejas y anidadas
- Incluye características avanzadas como índices geoespaciales, texto completo y agregaciones potentes
- Su arquitectura distribuida facilita la alta disponibilidad y la distribución geográfica de datos

Por otro lado, Python continúa siendo el lenguaje preferido para ciencia de datos y análisis por múltiples razones:

- Su sintaxis intuitiva y legible reduce la curva de aprendizaje
- Cuenta con un ecosistema sin rival de bibliotecas especializadas como pandas, NumPy y matplotlib
- Facilita la manipulación, transformación y visualización de datos de manera clara
- Permite integrar el análisis con aplicaciones web, microservicios y sistemas de automatización
- Su comunidad activa asegura soporte y evolución continua de herramientas

### La Sinergia Python + MongoDB

Cuando combinamos Python y MongoDB, aprovechamos lo mejor de ambas tecnologías. MongoDB nos proporciona un almacén de datos flexible, escalable y orientado a documentos, mientras que Python nos da la capacidad para:

1. Establecer conexiones robustas a la base de datos y realizar operaciones CRUD de forma programática
2. Transformar los documentos JSON/BSON a estructuras de datos de Python (como diccionarios y listas) de manera natural
3. Aplicar algoritmos avanzados de procesamiento y análisis estadístico sobre los datos extraídos
4. Generar visualizaciones expresivas que comuniquen patrones complejos
5. Implementar pipelines automatizados que cubran desde la ingestión hasta el análisis continuo de datos

Imaginemos este flujo de trabajo: sus datos semiestructurados (como registros de eventos, datos de sensores o información de redes sociales) se almacenan naturalmente en MongoDB, donde pueden realizar consultas iniciales utilizando su potente framework de agregación. Luego, extraen esos resultados a Python para análisis más sofisticados, visualizaciones interactivas o incluso para alimentar modelos de machine learning. Todo esto mientras aprovechan la escalabilidad inherente de MongoDB para manejar volúmenes crecientes de información. Esta integración proporciona un sistema completo y flexible para el análisis de datos modernos.

# Fundamentos de MongoDB con Docker

## Introducción a MongoDB

Comenzaremos explorando MongoDB, pero con un enfoque moderno: utilizaremos Docker para crear nuestro entorno de base de datos.

MongoDB es un sistema de gestión de bases de datos NoSQL orientado a documentos, lanzado en 2009, que ha revolucionado la forma en que almacenamos y consultamos datos. A diferencia de los sistemas relacionales tradicionales, MongoDB se distingue por:

- Su modelo de datos basado en documentos JSON/BSON que permite estructuras anidadas y variables
- Su esquema flexible que no requiere definición previa de estructuras
- Su capacidad para escalar horizontalmente mediante sharding nativo
- Su potente lenguaje de consulta y framework de agregación
- Su capacidad para manejar datos geoespaciales, series temporales y búsqueda de texto completo

## Instalación de MongoDB con Docker

Vamos a configurar paso a paso MongoDB usando Docker. Primero, verifiquemos que todos tengan Docker instalado:

```bash
docker --version

```

Si Docker está correctamente instalado, verán algo como `Docker version 20.10.21, build baeda1f`.

Ahora, vamos a descargar la imagen oficial de MongoDB y crear nuestro primer contenedor:

```bash
docker pull mongodb/mongodb-community-server
```

Esto descarga la versión 6.0 de MongoDB Community Edition. Ahora creemos un contenedor con esta imagen:

```bash
docker run --name mongodb-curso -e MONGO_INITDB_ROOT_USERNAME=usuario -e MONGO_INITDB_ROOT_PASSWORD=micontraseña -p 27017:27017 -d mongodb/mongodb-community-server

```

Analicemos este comando:

- `-name mongodb-curso`: Asigna un nombre a nuestro contenedor
- `e MONGO_INITDB_ROOT_USERNAME=usuario`: Establece el nombre de usuario administrador
- `e MONGO_INITDB_ROOT_PASSWORD=micontraseña`: Establece la contraseña del administrador
- `p 27017:27017`: Mapea el puerto 27017 del contenedor al puerto 27017 de nuestra máquina
- `d`: Ejecuta el contenedor en segundo plano

Para verificar que nuestro contenedor está funcionando:

```bash
docker ps

```

Deberían ver su contenedor `mongodb-curso` en la lista.

## Configuración inicial y acceso

Ahora vamos a acceder a nuestra instancia de MongoDB. Podemos hacerlo de dos maneras: usando mongosh desde el mismo contenedor o conectándonos desde nuestra máquina.

Primero, accedamos directamente desde el contenedor:

```bash
docker exec -it mongodb-curso mongosh --username usuario --password micontraseña

```

Esto nos da acceso al shell de MongoDB. Podemos salir escribiendo `exit`.

También podemos conectarnos utilizando MongoDB Compass, la interfaz gráfica oficial de MongoDB:

1. Descargar MongoDB Compass desde el sitio oficial
2. Conectar utilizando la URI: `mongodb://usuario:micontraseña@localhost:27017/admin`

Para crear una base de datos persistente, podemos montar un volumen Docker:

```bash
docker run --name mongodb-datos -e MONGO_INITDB_ROOT_USERNAME=usuario -e MONGO_INITDB_ROOT_PASSWORD=micontraseña -p 27017:27017 -v ~/mongodb-data:/data/db -d mongodb/mongodb-community-server

```

El parámetro `-v ~/mongodb-data:/data/db` monta un directorio local en el contenedor, asegurando que los datos persistan incluso si eliminamos el contenedor.

## Estructura y conceptos básicos de MongoDB

A diferencia de las bases de datos relacionales, MongoDB organiza sus datos de forma diferente:

- Una instancia MongoDB puede contener múltiples bases de datos
- Cada base de datos contiene múltiples colecciones (análogas a las tablas)
- Cada colección contiene documentos (análogos a las filas)
- Los documentos son estructuras BSON (Binary JSON) de esquema flexible

Vamos a crear nuestra primera base de datos y colección:

```jsx
// Crear/usar una base de datos
use analisis_datos

// Crear una colección implícitamente al insertar documentos
db.productos.insertOne({
  nombre: "Laptop ProX",
  categoria: "Electrónicos",
  precio: 1299.99,
  especificaciones: {
    procesador: "i7",
    ram: "16GB",
    almacenamiento: "512GB SSD"
  },
  colores_disponibles: ["Gris", "Negro", "Plata"],
  fecha_creacion: new Date()
})

```

Observen cómo MongoDB nos permite:

- Crear colecciones implícitamente
- Insertar documentos con estructuras anidadas (el campo "especificaciones")
- Incluir arrays dentro de documentos (el campo "colores_disponibles")
- Usar tipos de datos específicos (como Date)

## Operaciones CRUD básicas

Ahora, practiquemos las operaciones CRUD (Crear, Leer, Actualizar, Eliminar) fundamentales:

### Crear (Create)

```jsx
// Insertar un solo documento
db.clientes.insertOne({
  nombre: "Ana García",
  email: "ana@ejemplo.com",
  edad: 34,
  intereses: ["tecnología", "fotografía"],
  direccion: {
    ciudad: "Madrid",
    codigo_postal: "28001"
  }
})

// Insertar múltiples documentos
db.clientes.insertMany([
  {
    nombre: "Carlos Rodríguez",
    email: "carlos@ejemplo.com",
    edad: 28,
    intereses: ["deportes", "viajes"]
  },
  {
    nombre: "Elena Torres",
    email: "elena@ejemplo.com",
    edad: 45,
    intereses: ["arte", "cocina", "lectura"]
  }
])

```

### Leer (Read)

```jsx
// Encontrar todos los documentos en una colección
db.productos.find()

// Encontrar con condiciones
db.productos.find({ categoria: "Electrónicos" })

// Proyección (seleccionar solo ciertos campos)
db.productos.find({ precio: { $gt: 1000 } }, { nombre: 1, precio: 1, _id: 0 })

// Limitar resultados y ordenar
db.productos.find().sort({ precio: -1 }).limit(3)

```

### Actualizar (Update)

```jsx
// Actualizar un documento
db.productos.updateOne(
  { nombre: "Laptop ProX" },
  { $set: { precio: 1199.99, "especificaciones.ram": "32GB" } }
)

// Actualizar múltiples documentos
db.clientes.updateMany(
  { intereses: "tecnología" },
  { $set: { grupo: "tech_enthusiasts" } }
)

// Incrementar un valor
db.productos.updateOne(
  { nombre: "Laptop ProX" },
  { $inc: { stock: 5 } }
)

```

### Eliminar (Delete)

```jsx
// Eliminar un documento
db.clientes.deleteOne({ email: "ana@ejemplo.com" })

// Eliminar múltiples documentos
db.productos.deleteMany({ categoria: "Obsoletos" })

// Vaciar una colección
db.temporal.deleteMany({})

```

## Consultas avanzadas y el framework de agregación

MongoDB ofrece capacidades avanzadas de consulta a través de operadores y su framework de agregación:

### Operadores de consulta avanzados

```jsx
// Operadores de comparación
db.productos.find({ precio: { $lt: 500, $gt: 100 } })  // Precio entre 100 y 500

// Operadores lógicos
db.productos.find({
  $or: [
    { categoria: "Electrónicos" },
    { categoria: "Accesorios" }
  ],
  precio: { $lt: 300 }
})

// Consultas en arrays
db.clientes.find({ intereses: { $all: ["tecnología", "fotografía"] } })
db.productos.find({ "colores_disponibles.0": "Negro" })  // Primer elemento es Negro

// Consultas en documentos anidados
db.clientes.find({ "direccion.ciudad": "Madrid" })

```

### Framework de agregación

El framework de agregación es una herramienta poderosa para procesamiento y análisis de datos:

```jsx
// Pipeline de agregación básico
db.ventas.aggregate([
  // Etapa 1: Filtrar documentos
  { $match: { fecha: { $gte: new Date("2023-01-01") } } },

  // Etapa 2: Agrupar y calcular totales
  { $group: {
      _id: "$categoria",
      total_ventas: { $sum: "$monto" },
      promedio_venta: { $avg: "$monto" },
      conteo: { $count: {} }
  }},

  // Etapa 3: Ordenar resultados
  { $sort: { total_ventas: -1 } }
])

```

## Recapitulación y Docker-Compose

Para finalizar esta sección, veamos cómo hacer nuestra configuración aún más reproducible utilizando Docker Compose:

```yaml
# docker-compose.yml
version: '3'
services:
  mongodb:
    image: mongodb/mongodb-community-server
    environment:
      MONGO_INITDB_ROOT_USERNAME: usuario
      MONGO_INITDB_ROOT_PASSWORD: micontraseña
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
      - ./mongo-init.js:/docker-entrypoint-initdb.d/mongo-init.js

volumes:
  mongodb_data:

```

Podemos crear un archivo `mongo-init.js` para inicializar nuestra base de datos:

```jsx
// mongo-init.js
db = db.getSiblingDB('analisis_datos');

// Crear colecciones
db.createCollection('productos');
db.createCollection('clientes');
db.createCollection('ventas');

// Insertar datos iniciales
db.productos.insertMany([
  {
    nombre: "Laptop ProX",
    categoria: "Electrónicos",
    precio: 1299.99,
    stock: 10
  },
  {
    nombre: "Mouse Ergonómico",
    categoria: "Accesorios",
    precio: 49.99,
    stock: 25
  }
  // Más productos...
]);

// Más inserciones...

```

Para iniciar todo el entorno:

```bash
docker-compose up -d

```

## Resumen

En esta primera parte, hemos:

- Entendido qué es MongoDB y por qué es adecuado para ciertos tipos de datos y análisis
- Aprendido a utilizar Docker para configurar MongoDB de manera rápida y reproducible
- Explorado el modelo de datos basado en documentos de MongoDB y su esquema flexible
- Practicado operaciones CRUD básicas en MongoDB
- Introducido consultas avanzadas y el framework de agregación
- Configurado un entorno reproducible con Docker Compose

En la siguiente sección, veremos cómo conectar Python con esta base de datos MongoDB para comenzar a realizar análisis más avanzados.

# Parte 2: Conectando Python con MongoDB

## Introducción a la conexión Python-MongoDB

Ahora que tenemos nuestra base de datos MongoDB funcionando con Docker, vamos a explorar cómo Python puede interactuar con ella. Esta conexión es el puente que nos permitirá aprovechar la flexibilidad del modelo de documentos de MongoDB junto con las potentes capacidades analíticas de Python.

La comunicación entre Python y MongoDB nos abre un amplio abanico de posibilidades. Podemos insertar, consultar y procesar documentos directamente desde nuestro código, utilizar las bibliotecas analíticas de Python para análisis avanzados, y automatizar flujos de trabajo completos basados en datos semiestructurados.

## Bibliotecas Python para MongoDB

Existen varias bibliotecas en Python que nos permiten trabajar con MongoDB. Vamos a explorar las dos principales:

### PyMongo: El driver oficial

PyMongo es el driver oficial de MongoDB para Python, desarrollado y mantenido por el equipo de MongoDB. Proporciona una API que mapea directamente a los comandos y conceptos de MongoDB, lo que lo hace muy intuitivo si ya conoces MongoDB.

Primero, necesitamos instalarlo:

```bash
pip install pymongo

```

¿Cuándo usar PyMongo?

- Cuando necesitas control completo sobre la comunicación con MongoDB
- Para aplicaciones que requieren operaciones de baja latencia
- Cuando quieres utilizar características específicas de MongoDB
- Para desarrollar aplicaciones que requieren manipulación directa de documentos

### MongoEngine: El ODM para MongoDB

MongoEngine es un Mapeador Objeto-Documento (ODM) para MongoDB y Python. Permite definir esquemas estructurados para tus documentos y trabajar con ellos como si fueran objetos de Python.

Para instalarlo:

```bash
pip install mongoengine

```

¿Cuándo usar MongoEngine?

- Cuando prefieres un enfoque orientado a objetos para trabajar con documentos
- Si deseas validación de esquema y tipos a nivel de aplicación
- Para proyectos donde la claridad y la estructura del código son prioritarias
- Si estás más familiarizado con ORMs en el mundo relacional

### Comparación rápida

Veamos un ejemplo sencillo de la misma operación con ambas bibliotecas:

Con PyMongo (acceso directo):

```python
import pymongo

# Establecer conexión
client = pymongo.MongoClient("mongodb://usuario:micontraseña@localhost:27017/")
db = client["analisis_datos"]
coleccion = db["productos"]

# Insertar un documento
producto = {
    "nombre": "Teclado Mecánico",
    "categoria": "Accesorios",
    "precio": 129.99,
    "stock": 15
}
resultado = coleccion.insert_one(producto)
print(f"ID del nuevo documento: {resultado.inserted_id}")

# Consultar documentos
for producto in coleccion.find({"categoria": "Accesorios"}):
    print(f"{producto['nombre']}: ${producto['precio']}")

# Cerrar conexión
client.close()

```

Con MongoEngine (estilo ODM):

```python
from mongoengine import Document, StringField, FloatField, IntField, connect

# Conectar a la base de datos
connect('analisis_datos', host='mongodb://usuario:micontraseña@localhost:27017/analisis_datos')

# Definir una clase para documentos
class Producto(Document):
    nombre = StringField(required=True, max_length=200)
    categoria = StringField(required=True)
    precio = FloatField(required=True)
    stock = IntField(default=0)

    meta = {'collection': 'productos'}

# Crear y guardar un documento
nuevo_producto = Producto(
    nombre="Teclado Mecánico",
    categoria="Accesorios",
    precio=129.99,
    stock=15
)
nuevo_producto.save()
print(f"ID del nuevo documento: {nuevo_producto.id}")

# Consultar documentos
accesorios = Producto.objects(categoria="Accesorios")
for producto in accesorios:
    print(f"{producto.nombre}: ${producto.precio}")

```

Observen cómo MongoEngine proporciona una capa de abstracción orientada a objetos, mientras que PyMongo ofrece una interfaz más directa a MongoDB.

## Estableciendo conexiones a MongoDB desde Python

Ahora, vamos a profundizar en cómo establecer conexiones robustas a MongoDB desde Python.

### Conexión básica con PyMongo

```python
import pymongo
from pymongo import MongoClient

# Parámetros de conexión
params = {
    'host': 'localhost',
    'port': 27017,
    'username': 'usuario',
    'password': 'micontraseña',
    'authSource': 'admin'
}

# Establecer conexión
try:
    # Conexión con parámetros individuales
    client = MongoClient(
        host=params['host'],
        port=params['port'],
        username=params['username'],
        password=params['password'],
        authSource=params['authSource']
    )

    # Alternativamente, conexión con URI
    # client = MongoClient(f"mongodb://{params['username']}:{params['password']}@{params['host']}:{params['port']}/{params['authSource']}")

    # Verificar conexión
    client.admin.command('ping')
    print("Conexión establecida correctamente!")

    # Operaciones con la base de datos
    # ...

    # Cerrar conexión
    client.close()
except Exception as e:
    print(f"Error al conectar: {e}")

```

### Gestión segura de credenciales

En un entorno real, nunca debemos codificar las credenciales directamente en el código:

1. Variables de entorno:

```python
import os
from pymongo import MongoClient

client = MongoClient(
    host=os.environ.get('MONGO_HOST', 'localhost'),
    port=int(os.environ.get('MONGO_PORT', 27017)),
    username=os.environ.get('MONGO_USER'),
    password=os.environ.get('MONGO_PASSWORD'),
    authSource=os.environ.get('MONGO_AUTH_DB', 'admin')
)

```

1. Archivo de configuración:

```python
import configparser
from pymongo import MongoClient

config = configparser.ConfigParser()
config.read('config.ini')

mongo_section = config['mongodb']
client = MongoClient(
    host=mongo_section.get('host', 'localhost'),
    port=mongo_section.getint('port', 27017),
    username=mongo_section.get('username'),
    password=mongo_section.get('password'),
    authSource=mongo_section.get('authSource', 'admin')
)

```

1. Gestor de secretos (en entornos de producción):

```python
# Ejemplo con AWS Secrets Manager
import boto3
import json
from pymongo import MongoClient

def get_secret():
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId='mongodb-credentials')
    return json.loads(response['SecretString'])

secret = get_secret()
client = MongoClient(
    host=secret['host'],
    port=secret['port'],
    username=secret['username'],
    password=secret['password'],
    authSource=secret.get('authSource', 'admin')
)

```

### Usando context managers para conexiones

Al igual que con PostgreSQL, podemos usar context managers para gestionar nuestras conexiones:

```python
from pymongo import MongoClient
from contextlib import contextmanager

@contextmanager
def get_mongo_client(connection_string):
    client = MongoClient(connection_string)
    try:
        yield client
    finally:
        client.close()

# Uso del context manager
with get_mongo_client("mongodb://usuario:micontraseña@localhost:27017/") as client:
    db = client.analisis_datos
    productos = db.productos.find({"precio": {"$gt": 100}}).limit(5)
    for producto in productos:
        print(f"{producto['nombre']}: ${producto['precio']}")

```

## Operaciones CRUD desde Python

Veamos cómo realizar operaciones CRUD (Crear, Leer, Actualizar, Eliminar) desde Python.

### Crear (Insert)

```python
from pymongo import MongoClient

client = MongoClient("mongodb://usuario:micontraseña@localhost:27017/")
db = client.analisis_datos

# Insertar un solo documento
resultado = db.productos.insert_one({
    "nombre": "Monitor UltraHD",
    "categoria": "Electrónicos",
    "precio": 349.99,
    "stock": 8,
    "especificaciones": {
        "tamaño": "27 pulgadas",
        "resolución": "3840x2160",
        "tipo": "IPS"
    }
})
print(f"ID del documento insertado: {resultado.inserted_id}")

# Insertar múltiples documentos
nuevos_clientes = [
    {
        "nombre": "David López",
        "email": "david@ejemplo.com",
        "edad": 31,
        "intereses": ["tecnología", "música", "fotografía"]
    },
    {
        "nombre": "Laura Martínez",
        "email": "laura@ejemplo.com",
        "edad": 27,
        "intereses": ["viajes", "gastronomía"]
    }
]
resultado = db.clientes.insert_many(nuevos_clientes)
print(f"IDs de documentos insertados: {resultado.inserted_ids}")

client.close()

```

### Leer (Read)

```python
# Encontrar un solo documento
producto = db.productos.find_one({"nombre": "Monitor UltraHD"})
if producto:
    print(f"Producto encontrado: {producto['nombre']} - ${producto['precio']}")

# Encontrar múltiples documentos con filtros
query = {"categoria": "Electrónicos", "precio": {"$lt": 500}}
productos = db.productos.find(query).sort("precio", -1)
print("Productos electrónicos por debajo de $500:")
for producto in productos:
    print(f"- {producto['nombre']}: ${producto['precio']}")

# Proyección: seleccionar solo ciertos campos
productos = db.productos.find(
    {"stock": {"$gt": 0}},
    {"nombre": 1, "precio": 1, "stock": 1, "_id": 0}
)
print("\nProductos en stock (solo nombre, precio y cantidad):")
for producto in productos:
    print(f"- {producto['nombre']}: ${producto['precio']} ({producto['stock']} unidades)")

# Consultas avanzadas con operadores
clientes_tech = db.clientes.find({"intereses": {"$in": ["tecnología"]}})
print("\nClientes interesados en tecnología:")
for cliente in clientes_tech:
    print(f"- {cliente['nombre']} ({cliente['email']})")

```

### Actualizar (Update)

```python
# Actualizar un solo documento
resultado = db.productos.update_one(
    {"nombre": "Monitor UltraHD"},
    {"$set": {"precio": 329.99, "promocion": True}}
)
print(f"Documentos modificados: {resultado.modified_count}")

# Actualizar múltiples documentos
resultado = db.productos.update_many(
    {"categoria": "Electrónicos", "promocion": {"$exists": False}},
    {"$set": {"promocion": False}}
)
print(f"Electrónicos actualizados: {resultado.modified_count}")

# Operadores de actualización
# $inc para incrementar valores
db.productos.update_one(
    {"nombre": "Monitor UltraHD"},
    {"$inc": {"stock": -1}}  # Reducir stock en 1
)

# $push para añadir a arrays
db.clientes.update_one(
    {"email": "laura@ejemplo.com"},
    {"$push": {"intereses": "deportes"}}
)

# Upsert (insertar si no existe)
db.productos.update_one(
    {"sku": "MON-UHD-27"},  # Documento que quizás no existe
    {"$set": {"nombre": "Monitor UltraHD", "sku": "MON-UHD-27"}},
    upsert=True
)

```

### Eliminar (Delete)

```python
# Eliminar un solo documento
resultado = db.clientes.delete_one({"email": "cliente_obsoleto@ejemplo.com"})
print(f"Documentos eliminados: {resultado.deleted_count}")

# Eliminar múltiples documentos
resultado = db.productos.delete_many({"stock": 0, "descontinuado": True})
print(f"Productos obsoletos eliminados: {resultado.deleted_count}")

# Eliminar colección
db.temp_collection.drop()

```

## Consultas avanzadas y aggregation framework

El poder analítico de MongoDB reside principalmente en su aggregation framework, que permite realizar transformaciones y análisis complejos de los datos directamente en la base de datos.

MongoDB destaca por sus capacidades de consulta avanzada y su framework de agregación, que podemos utilizar desde Python:

### Consultas avanzadas

```python
# Consultas con expresiones regulares
import re
regex_pattern = re.compile('ultra', re.IGNORECASE)
productos = db.productos.find({"nombre": regex_pattern})

# Consultas geoespaciales
# Asumiendo que tenemos una colección de tiendas con coordenadas
db.tiendas.create_index([("ubicacion", "2dsphere")])
tiendas_cercanas = db.tiendas.find({
   "ubicacion": {
     "$near": {
       "$geometry": {
         "type": "Point",
         "coordinates": [-73.9667, 40.78]
       },
       "$maxDistance": 5000  # 5km
     }
   }
})

# Consultas de texto completo
db.productos.create_index([("descripcion", "text")])
productos = db.productos.find({"$text": {"$search": "alta calidad resistente"}})

```

### Framework de agregación

El framework de agregación es la herramienta más potente de MongoDB para análisis:

```python
# Pipeline básico: ventas por categoría
pipeline = [
    # Etapa 1: Filtrar solo ventas completadas
    {"$match": {"estado": "completado"}},

    # Etapa 2: Agrupar por categoría
    {"$group": {
        "_id": "$categoria_producto",
        "total_ventas": {"$sum": "$monto"},
        "cantidad_ventas": {"$sum": 1},
        "promedio_venta": {"$avg": "$monto"}
    }},

    # Etapa 3: Ordenar por total de ventas
    {"$sort": {"total_ventas": -1}}
]

resultados = db.ventas.aggregate(pipeline)
for resultado in resultados:
    print(f"Categoría: {resultado['_id']}")
    print(f"  Total ventas: ${resultado['total_ventas']:,.2f}")
    print(f"  Cantidad: {resultado['cantidad_ventas']}")
    print(f"  Promedio: ${resultado['promedio_venta']:,.2f}")

```

Agregación más compleja: análisis de ventas por tiempo y geografía:

```python
pipeline = [
    # Etapa 1: Añadir campos calculados para fecha
    {"$addFields": {
        "mes": {"$month": "$fecha_venta"},
        "año": {"$year": "$fecha_venta"}
    }},

    # Etapa 2: Agrupar por región, año y mes
    {"$group": {
        "_id": {
            "region": "$region_cliente",
            "año": "$año",
            "mes": "$mes"
        },
        "total_ventas": {"$sum": "$monto"},
        "cantidad_transacciones": {"$sum": 1},
        "productos_vendidos": {"$sum": "$cantidad_productos"}
    }},

    # Etapa 3: Agrupar de nuevo para obtener totales anuales por región
    {"$group": {
        "_id": {
            "region": "$_id.region",
            "año": "$_id.año"
        },
        "ventas_mensuales": {"$push": {
            "mes": "$_id.mes",
            "ventas": "$total_ventas",
            "transacciones": "$cantidad_transacciones"
        }},
        "total_anual": {"$sum": "$total_ventas"},
        "promedio_mensual": {"$avg": "$total_ventas"}
    }},

    # Etapa 4: Ordenar por región y año
    {"$sort": {"_id.region": 1, "_id.año": 1}}
]

resultados = db.ventas.aggregate(pipeline)
for resultado in resultados:
    region = resultado["_id"]["region"]
    año = resultado["_id"]["año"]
    print(f"Región: {region}, Año: {año}")
    print(f"  Total anual: ${resultado['total_anual']:,.2f}")
    print(f"  Promedio mensual: ${resultado['promedio_mensual']:,.2f}")
    print("  Desglose mensual:")
    for mes in sorted(resultado["ventas_mensuales"], key=lambda x: x["mes"]):
        print(f"    Mes {mes['mes']}: ${mes['ventas']:,.2f} ({mes['transacciones']} transacciones)")
    print()

```