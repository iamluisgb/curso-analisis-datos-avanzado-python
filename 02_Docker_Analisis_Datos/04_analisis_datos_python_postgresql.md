# Análisis de Datos con Python y PostgreSQL
## Introducción

### ¿Por qué Python y PostgreSQL?

Ahora bien, ¿por qué hemos elegido específicamente Python y PostgreSQL para este curso? Estas herramientas representan una combinación excepcionalmente poderosa que aborda los dos aspectos fundamentales del análisis de datos: almacenamiento/gestión y procesamiento/análisis.

PostgreSQL es uno de los sistemas de gestión de bases de datos más avanzados, robustos y confiables del mundo. A diferencia de otras bases de datos, PostgreSQL ofrece:

- Una capacidad excepcional para manejar datos estructurados con integridad y consistencia
- Soporte para tipos de datos avanzados, incluyendo JSON, arrays y datos geoespaciales
- Potentes capacidades analíticas directamente en la base de datos
- Escalabilidad para manejar desde pequeños conjuntos de datos hasta terabytes de información

Por otro lado, Python se ha convertido en el lenguaje de facto para ciencia de datos y análisis por razones muy claras:

- Su sintaxis clara y legible reduce la barrera de entrada
- Cuenta con un ecosistema extraordinario de bibliotecas especializadas como pandas, NumPy y matplotlib
- Ofrece flexibilidad para manipular, transformar y visualizar datos de manera intuitiva
- Permite integrar el análisis de datos con aplicaciones, servicios web y sistemas de automatización

### La Sinergia Python + PostgreSQL

Cuando combinamos Python y PostgreSQL, obtenemos lo mejor de ambos mundos. PostgreSQL nos proporciona un lugar seguro, estructurado y eficiente para almacenar nuestros datos, con la potencia del lenguaje SQL para realizar consultas complejas. Python nos da la flexibilidad para:

1. Conectarnos a la base de datos y extraer justo la información que necesitamos
2. Transformar y limpiar los datos para prepararlos para el análisis
3. Aplicar técnicas avanzadas de análisis estadístico o incluso machine learning
4. Visualizar los resultados de manera clara y comunicativa
5. Automatizar todo el proceso para que pueda repetirse sistemáticamente

Imaginen este flujo de trabajo: sus datos transaccionales se almacenan de manera segura en PostgreSQL, donde pueden realizar consultas complejas para extraer patrones iniciales. Luego, traen esos resultados a Python para un análisis más profundo, visualizaciones personalizadas o incluso para entrenar modelos predictivos. Finalmente, pueden guardar los resultados de vuelta en PostgreSQL o generar reportes automatizados. Esta integración crea un ciclo completo de análisis de datos tremendamente poderoso.

# Fundamentos de PostgreSQL con Docker

## Introducción a PostgreSQL

Bienvenidos a la primera parte de nuestro curso. Ahora vamos a sumergirnos en PostgreSQL, pero con un enfoque moderno: utilizaremos Docker para crear nuestro entorno de base de datos.

PostgreSQL es un sistema de gestión de bases de datos relacional de código abierto con más de 30 años de desarrollo. A diferencia de otros sistemas, PostgreSQL destaca por:

- Su arquitectura extensible que permite crear tipos de datos personalizados
- Su cumplimiento estricto con los estándares SQL
- Su capacidad para manejar cargas de trabajo que van desde pequeñas aplicaciones hasta datawarehouses empresariales
- Funcionalidades avanzadas como índices de texto completo, datos geoespaciales y soporte para JSON

## Instalación de PostgreSQL con Docker

Vamos a ver paso a paso cómo configurar PostgreSQL usando Docker. Primero, asegurémonos de que todos tengan Docker instalado. Pueden verificarlo abriendo una terminal y ejecutando:

```bash
docker --version

```

Si Docker está instalado correctamente, verán algo como `Docker version 20.10.21, build baeda1f`.

Ahora, vamos a descargar la imagen oficial de PostgreSQL y crear nuestro primer contenedor:

```bash
docker pull postgres:14

```

Esto descarga la versión 14 de PostgreSQL. Ahora creemos un contenedor con esta imagen:

```bash
docker run --name postgres-curso -e POSTGRES_PASSWORD=micontraseña -p 5432:5432 -d postgres:14

```

Analicemos este comando:

- `-name postgres-curso`: Asigna un nombre a nuestro contenedor
- `e POSTGRES_PASSWORD=micontraseña`: Establece la contraseña del usuario postgres
- `p 5432:5432`: Mapea el puerto 5432 del contenedor al puerto 5432 de nuestra máquina
- `d`: Ejecuta el contenedor en segundo plano

Para verificar que nuestro contenedor está funcionando:

```bash
docker ps

```

Deberían ver su contenedor `postgres-curso` en la lista.

## Configuración inicial y acceso

Ahora vamos a acceder a nuestra instancia de PostgreSQL. Podemos hacerlo de dos maneras: usando psql desde el mismo contenedor o conectándonos desde nuestro host.

Primero, accedamos directamente desde el contenedor:

```bash
docker exec -it postgres-curso psql -U postgres

```

Esto nos da acceso a la línea de comandos de PostgreSQL. Podemos salir escribiendo `\q`.

También podemos conectarnos utilizando cualquier cliente SQL. Si tienen instalado psql en su sistema:

```bash
psql -h localhost -p 5432 -U postgres

```

O pueden usar herramientas gráficas como pgAdmin, DBeaver o DataGrip configurando los mismos parámetros de conexión.

Para crear una base de datos persistente, podemos montar un volumen Docker:

```bash
docker run --name postgres-datos -e POSTGRES_PASSWORD=micontraseña -p 5432:5432 -v ~/postgres-data:/var/lib/postgresql/data -d postgres:14

```

El parámetro `-v ~/postgres-data:/var/lib/postgresql/data` monta un directorio local en el contenedor, asegurando que los datos persistan incluso si eliminamos el contenedor.

## Creación y estructura de bases de datos

Ahora que tenemos nuestro servidor PostgreSQL funcionando, vamos a crear nuestra primera base de datos:

```sql
CREATE DATABASE analisis_datos;

```

Conectémonos a esta base de datos:

```sql
\c analisis_datos

```

La estructura de PostgreSQL se organiza jerárquicamente:

- Una instancia PostgreSQL puede contener múltiples bases de datos
- Cada base de datos contiene múltiples esquemas (el predeterminado es "public")
- Cada esquema contiene objetos como tablas, vistas, funciones, etc.

Vamos a crear un esquema específico para nuestro análisis:

```sql
CREATE SCHEMA ventas;

```

Y ahora, creemos algunas tablas dentro de este esquema:

```sql
CREATE TABLE ventas.productos (
    id_producto SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    categoria VARCHAR(50),
    precio NUMERIC(10,2) NOT NULL,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE ventas.clientes (
    id_cliente SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE ventas.transacciones (
    id_transaccion SERIAL PRIMARY KEY,
    id_cliente INTEGER REFERENCES ventas.clientes(id_cliente),
    id_producto INTEGER REFERENCES ventas.productos(id_producto),
    cantidad INTEGER NOT NULL,
    monto_total NUMERIC(10,2) NOT NULL,
    fecha_transaccion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

```

Observen los elementos clave:

- `SERIAL` es un tipo autoincremental
- `PRIMARY KEY` establece la clave primaria
- `REFERENCES` crea una clave foránea
- `DEFAULT CURRENT_TIMESTAMP` establece un valor predeterminado

## Trabajando con datos: Consultas SQL básicas

Ahora que tenemos nuestra estructura, vamos a insertar algunos datos y practicar consultas básicas:

Insertemos datos en nuestras tablas:

```sql
-- Insertar productos
INSERT INTO ventas.productos (nombre, categoria, precio) VALUES
('Laptop ProX', 'Electrónicos', 1299.99),
('Mouse Ergonómico', 'Accesorios', 49.99),
('Monitor UltraHD', 'Electrónicos', 349.50),
('Teclado Mecánico', 'Accesorios', 129.99),
('Disco SSD 1TB', 'Almacenamiento', 159.99);

-- Insertar clientes
INSERT INTO ventas.clientes (nombre, email) VALUES
('Ana García', 'ana@ejemplo.com'),
('Carlos Rodríguez', 'carlos@ejemplo.com'),
('Elena Torres', 'elena@ejemplo.com');

-- Insertar transacciones
INSERT INTO ventas.transacciones (id_cliente, id_producto, cantidad, monto_total) VALUES
(1, 1, 1, 1299.99),
(1, 2, 2, 99.98),
(2, 3, 1, 349.50),
(3, 1, 1, 1299.99),
(2, 4, 1, 129.99),
(3, 5, 2, 319.98);

```

Ahora, practiquemos algunas consultas básicas:

1. Seleccionar todos los productos:

```sql
SELECT * FROM ventas.productos;

```

1. Filtrar con WHERE:

```sql
SELECT nombre, precio FROM ventas.productos
WHERE categoria = 'Electrónicos' AND precio > 500;

```

1. Ordenar resultados:

```sql
SELECT nombre, precio FROM ventas.productos
ORDER BY precio DESC;

```

1. Agrupar y agregar:

```sql
SELECT categoria, COUNT(*) as cantidad, AVG(precio) as precio_promedio
FROM ventas.productos
GROUP BY categoria;

```

1. Unir tablas con JOIN:

```sql
SELECT c.nombre as cliente, p.nombre as producto, t.cantidad, t.monto_total
FROM ventas.transacciones t
JOIN ventas.clientes c ON t.id_cliente = c.id_cliente
JOIN ventas.productos p ON t.id_producto = p.id_producto;

```

## Recapitulación y Docker-Compose

Para finalizar esta sección, vamos a ver cómo podemos hacer nuestra configuración aún más reproducible utilizando Docker Compose. Esto nos permitirá definir toda nuestra infraestructura en un archivo:

```yaml
# docker-compose.yml
version: '3'
services:
  postgres:
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: micontraseña
      POSTGRES_DB: analisis_datos
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-scripts:/docker-entrypoint-initdb.d

volumes:
  postgres_data:

```

Podemos crear un directorio `init-scripts` con nuestros scripts SQL de inicialización:

```sql
-- 01-schema.sql
CREATE SCHEMA ventas;

CREATE TABLE ventas.productos (
    id_producto SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    categoria VARCHAR(50),
    precio NUMERIC(10,2) NOT NULL,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- resto de las tablas y datos...

```

Para iniciar todo el entorno:

```bash
docker-compose up -d

```

Con esto, cualquier persona puede reproducir exactamente nuestro entorno de trabajo con un solo comando.

## Resumen

En esta primera parte, hemos:

- Entendido qué es PostgreSQL y por qué es adecuado para análisis de datos
- Aprendido a utilizar Docker para configurar PostgreSQL de manera rápida y reproducible
- Creado una base de datos estructurada con esquemas y tablas relacionadas
- Practicado consultas SQL fundamentales que serán la base para nuestros análisis
- Visto cómo hacer que nuestro entorno sea totalmente reproducible con Docker Compose

En la siguiente sección, veremos cómo conectar Python con esta base de datos que hemos creado para comenzar a realizar análisis más avanzados.


# Conectando Python con PostgreSQL

## Introducción a la conexión Python-PostgreSQL

Ahora que tenemos nuestra base de datos PostgreSQL funcionando con Docker, vamos a explorar cómo Python puede comunicarse con ella. Esta conexión es lo que nos permitirá combinar la potencia de ambas herramientas para realizar análisis de datos avanzados.

La capacidad de Python para conectarse con PostgreSQL nos abre un mundo de posibilidades. Podemos ejecutar consultas SQL desde nuestro código, procesar los resultados con las bibliotecas analíticas de Python, y automatizar todo tipo de flujos de trabajo con datos. Es el puente que une el almacenamiento estructurado con el análisis flexible.

## Bibliotecas Python para PostgreSQL

Existen varias bibliotecas en Python que nos permiten interactuar con PostgreSQL. Vamos a explorar las dos principales:

### psycopg2: La conexión directa

Psycopg2 es la biblioteca de conexión PostgreSQL más popular para Python. Es un adaptador de base de datos PostgreSQL implementado en C, lo que la hace muy eficiente. Ofrece una API que sigue de cerca la especificación de Python DB-API 2.0, lo que significa que si conoces otras bibliotecas de bases de datos en Python, te resultará familiar.

Primero, necesitamos instalarla:

```bash
pip install psycopg2-binary

```

Nota: Usamos la versión "binary" para evitar tener que compilar el código C, lo que requeriría herramientas de desarrollo adicionales.

¿Cuándo usar psycopg2?

- Cuando necesitas control detallado sobre la conexión a la base de datos
- Para aplicaciones de alto rendimiento
- Cuando quieres estar más cerca del SQL nativo
- Para operaciones que requieren características específicas de PostgreSQL

### SQLAlchemy: El ORM completo

SQLAlchemy es mucho más que un conector de base de datos; es un ORM (Object-Relational Mapper) completo que permite trabajar con bases de datos en un estilo más orientado a objetos. Con SQLAlchemy, podemos representar tablas como clases y registros como objetos.

Para instalarlo:

```bash
pip install sqlalchemy psycopg2-binary

```

Necesitamos también psycopg2 porque SQLAlchemy lo usa internamente para conectarse a PostgreSQL.

¿Cuándo usar SQLAlchemy?

- Para aplicaciones más complejas donde quieres abstraer el SQL
- Cuando prefieres un enfoque orientado a objetos
- Para proyectos grandes donde la portabilidad entre diferentes bases de datos es importante
- Cuando trabajas en un equipo con desarrolladores menos familiarizados con SQL

### Comparación rápida

Veamos un ejemplo sencillo de la misma operación con ambas bibliotecas:

Con psycopg2 (estilo directo):

```python
import psycopg2

conn = psycopg2.connect("host=localhost dbname=analisis_datos user=postgres password=micontraseña")
cur = conn.cursor()
cur.execute("SELECT nombre, precio FROM ventas.productos WHERE categoria = %s", ("Electrónicos",))
productos = cur.fetchall()
for producto in productos:
    print(producto)
cur.close()
conn.close()

```

Con SQLAlchemy (estilo ORM):

```python
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String, Float, select

engine = create_engine("postgresql://postgres:micontraseña@localhost:5432/analisis_datos")
metadata = MetaData(schema="ventas")
productos = Table('productos', metadata,
                 Column('id_producto', Integer, primary_key=True),
                 Column('nombre', String),
                 Column('categoria', String),
                 Column('precio', Float))

with engine.connect() as conn:
    query = select(productos.c.nombre, productos.c.precio).where(productos.c.categoria == "Electrónicos")
    result = conn.execute(query)
    for row in result:
        print(row)

```

Como pueden ver, SQLAlchemy requiere más código inicial pero ofrece una aproximación más "pythónica" a largo plazo.

## Estableciendo conexiones a la base de datos

Ahora, vamos a ver en detalle cómo establecer conexiones robustas a PostgreSQL desde Python.

### Conexión básica con psycopg2

```python
import psycopg2

# Parámetros de conexión
params = {
    'host': 'localhost',
    'port': '5432',
    'database': 'analisis_datos',
    'user': 'postgres',
    'password': 'micontraseña'
}

# Establecer conexión
try:
    conn = psycopg2.connect(**params)
    print("Conexión establecida correctamente!")

    # Crear un cursor
    cur = conn.cursor()

    # Operaciones con la base de datos
    # ...

    # Cerrar cursor y conexión
    cur.close()
    conn.close()
except Exception as e:
    print(f"Error al conectar: {e}")

```

### Gestión segura de credenciales

En un entorno real, nunca debemos codificar las credenciales directamente en el código. Veamos algunas alternativas:

1. Variables de entorno:

```python
import os
import psycopg2

conn = psycopg2.connect(
    host=os.environ.get('DB_HOST', 'localhost'),
    port=os.environ.get('DB_PORT', '5432'),
    database=os.environ.get('DB_NAME', 'analisis_datos'),
    user=os.environ.get('DB_USER', 'postgres'),
    password=os.environ.get('DB_PASSWORD', 'micontraseña')
)

```

1. Archivo de configuración:

```python
import configparser
import psycopg2

config = configparser.ConfigParser()
config.read('config.ini')

conn = psycopg2.connect(
    host=config['postgresql']['host'],
    port=config['postgresql']['port'],
    database=config['postgresql']['database'],
    user=config['postgresql']['user'],
    password=config['postgresql']['password']
)

```

1. Gestor de secretos (en entornos de producción):

```python
# Ejemplo con AWS Secrets Manager
import boto3
import json
import psycopg2

def get_secret():
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId='postgres-credentials')
    return json.loads(response['SecretString'])

secret = get_secret()
conn = psycopg2.connect(
    host=secret['host'],
    port=secret['port'],
    database=secret['database'],
    user=secret['username'],
    password=secret['password']
)

```

### Usando context managers para conexiones

En Python, es una buena práctica usar context managers (`with`) para gestionar recursos como conexiones de base de datos:

```python
import psycopg2
from psycopg2.extras import RealDictCursor

params = {...}  # Parámetros de conexión

with psycopg2.connect(**params) as conn:
    with conn.cursor(cursor_factory=RealDictCursor) as cur:
        cur.execute("SELECT * FROM ventas.productos LIMIT 5")
        productos = cur.fetchall()
        for producto in productos:
            print(f"{producto['nombre']}: ${producto['precio']}")

```

Esto asegura que la conexión se cierre correctamente incluso si ocurre alguna excepción.

## Ejecución de consultas SQL desde Python

Ahora veamos cómo ejecutar consultas SQL y manejar los resultados.

### Consultas básicas

```python
import psycopg2

with psycopg2.connect(...) as conn:
    with conn.cursor() as cur:
        # Consulta simple
        cur.execute("SELECT * FROM ventas.productos")
        productos = cur.fetchall()

        # Limitando resultados
        cur.execute("SELECT * FROM ventas.productos LIMIT 3")
        primeros_tres = cur.fetchall()

        # Ordenando resultados
        cur.execute("SELECT * FROM ventas.productos ORDER BY precio DESC")
        ordenados = cur.fetchall()

```

### Consultas parametrizadas (prevención de SQL Injection)

Es crucial usar consultas parametrizadas para evitar ataques de SQL Injection:

```python
# NUNCA HAGAS ESTO (vulnerable a SQL Injection):
categoria = "Electrónicos"
cur.execute(f"SELECT * FROM ventas.productos WHERE categoria = '{categoria}'")

# En su lugar, haz esto:
cur.execute("SELECT * FROM ventas.productos WHERE categoria = %s", (categoria,))

```

Psycopg2 usa el marcador de posición `%s` para todos los tipos de datos, a diferencia de otros conectores que pueden usar `?` o `:nombre`.

### Diferentes formas de obtener resultados

```python
# Obtener todos los resultados de una vez
cur.execute("SELECT * FROM ventas.productos")
todos = cur.fetchall()  # Lista de tuplas

# Obtener un solo resultado
cur.execute("SELECT COUNT(*) FROM ventas.productos")
count = cur.fetchone()[0]  # Tupla con un solo elemento

# Obtener resultados en lotes
cur.execute("SELECT * FROM ventas.transacciones")
while True:
    batch = cur.fetchmany(size=2)  # Obtener 2 a la vez
    if not batch:
        break
    for row in batch:
        print(row)

```

### Diferentes formatos de resultados

Por defecto, psycopg2 devuelve tuplas, pero podemos cambiar esto:

```python
# Usando el cursor RealDictCursor para obtener diccionarios
from psycopg2.extras import RealDictCursor

with conn.cursor(cursor_factory=RealDictCursor) as cur:
    cur.execute("SELECT * FROM ventas.productos LIMIT 3")
    productos = cur.fetchall()
    # productos es una lista de diccionarios
    print(productos[0]['nombre'])  # Acceso por nombre de columna

# Usando el cursor NamedTupleCursor para obtener namedtuples
from psycopg2.extras import NamedTupleCursor

with conn.cursor(cursor_factory=NamedTupleCursor) as cur:
    cur.execute("SELECT * FROM ventas.productos LIMIT 3")
    productos = cur.fetchall()
    # productos es una lista de namedtuples
    print(productos[0].nombre)  # Acceso por atributo

```

## Transacciones y control de errores

Las transacciones son fundamentales en las bases de datos para mantener la integridad de los datos.

### Transacciones básicas

En psycopg2, las transacciones se inician automáticamente y se confirman o revierten con `commit()` o `rollback()`:

```python
conn = psycopg2.connect(...)
try:
    cur = conn.cursor()

    # Múltiples operaciones en una transacción
    cur.execute("INSERT INTO ventas.clientes (nombre, email) VALUES (%s, %s)",
                ("David López", "david@ejemplo.com"))

    id_cliente = cur.fetchone()[0]  # Obtenemos el ID generado

    cur.execute("INSERT INTO ventas.transacciones (id_cliente, id_producto, cantidad, monto_total) VALUES (%s, %s, %s, %s)",
                (id_cliente, 1, 2, 2599.98))

    # Si llegamos aquí sin errores, confirmamos la transacción
    conn.commit()
    print("Transacción completada con éxito")

except Exception as e:
    # Si hay algún error, revertimos la transacción
    conn.rollback()
    print(f"Error en la transacción: {e}")

finally:
    # Siempre cerramos la conexión
    conn.close()

```

### Usando context managers para transacciones

SQLAlchemy proporciona una forma elegante de manejar transacciones:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine("postgresql://postgres:micontraseña@localhost:5432/analisis_datos")
Session = sessionmaker(bind=engine)

# Usando una sesión con transacción
with Session() as session:
    try:
        # Operaciones de la base de datos
        # ...

        # Confirmamos si todo está bien
        session.commit()
    except:
        # Revertimos si hay algún error
        session.rollback()
        raise

```

### Manejo de excepciones específicas de PostgreSQL

Psycopg2 proporciona excepciones específicas para diferentes errores:

```python
import psycopg2
from psycopg2 import errors

try:
    conn = psycopg2.connect(...)
    cur = conn.cursor()

    # Intentamos una operación que podría fallar
    cur.execute("INSERT INTO ventas.clientes (nombre, email) VALUES (%s, %s)",
                ("Ana García", "ana@ejemplo.com"))

    conn.commit()

except errors.UniqueViolation:
    print("Error: El email ya existe en la base de datos")
    conn.rollback()

except errors.ForeignKeyViolation:
    print("Error: La referencia a otra tabla es inválida")
    conn.rollback()

except Exception as e:
    print(f"Error inesperado: {e}")
    conn.rollback()

finally:
    conn.close()

```

## Caso práctico: Integrando todo

Vamos a crear una pequeña clase que encapsule todas las operaciones que hemos visto:

```python
import psycopg2
from psycopg2.extras import RealDictCursor
import os

class PostgresManager:
    def __init__(self, host='localhost', port='5432',
                 dbname='analisis_datos', user='postgres', password=None):
        self.connection_params = {
            'host': host,
            'port': port,
            'dbname': dbname,
            'user': user,
            'password': password or os.environ.get('DB_PASSWORD')
        }
        self.conn = None

    def connect(self):
        """Establece una conexión a la base de datos."""
        if self.conn is None or self.conn.closed:
            self.conn = psycopg2.connect(**self.connection_params)
        return self.conn

    def execute_query(self, query, params=None, fetch=True):
        """Ejecuta una consulta y opcionalmente devuelve los resultados."""
        with self.connect() as conn:
            with conn.cursor(cursor_factory=RealDictCursor) as cursor:
                cursor.execute(query, params or ())
                if fetch:
                    return cursor.fetchall()
                return None

    def execute_transaction(self, queries):
        """Ejecuta múltiples consultas en una transacción."""
        conn = self.connect()
        try:
            with conn.cursor() as cursor:
                for query, params in queries:
                    cursor.execute(query, params or ())
                conn.commit()
                return True
        except Exception as e:
            conn.rollback()
            print(f"Error en la transacción: {e}")
            return False

    def close(self):
        """Cierra la conexión a la base de datos."""
        if self.conn and not self.conn.closed:
            self.conn.close()

```

Y ahora, veamos cómo usar esta clase:

```python
# Crear una instancia
db = PostgresManager(password='micontraseña')

# Consulta simple
productos = db.execute_query("SELECT * FROM ventas.productos")
for p in productos:
    print(f"{p['nombre']}: ${p['precio']}")

# Consulta con parámetros
electrónicos = db.execute_query(
    "SELECT * FROM ventas.productos WHERE categoria = %s",
    ("Electrónicos",)
)

# Transacción con múltiples operaciones
transaction_queries = [
    ("INSERT INTO ventas.clientes (nombre, email) VALUES (%s, %s)",
     ("Miguel Ángel", "miguel@ejemplo.com")),
    ("INSERT INTO ventas.transacciones (id_cliente, id_producto, cantidad, monto_total) VALUES (%s, %s, %s, %s)",
     (4, 5, 1, 159.99))
]
success = db.execute_transaction(transaction_queries)

# Cerrar la conexión al finalizar
db.close()

```

## Resumen

En esta segunda parte, hemos:

1. Explorado las principales bibliotecas para conectar Python con PostgreSQL: psycopg2 para acceso directo y SQLAlchemy para un enfoque ORM.
2. Aprendido a establecer conexiones seguras gestionando adecuadamente las credenciales.
3. Practicado la ejecución de consultas SQL, incluyendo consultas parametrizadas para prevenir SQL Injection.
4. Visto diferentes formas de obtener y procesar los resultados de las consultas.
5. Entendido cómo manejar transacciones y errores para mantener la integridad de los datos.
6. Creado una clase reutilizable que encapsula todas estas funcionalidades.

Esta conexión entre Python y PostgreSQL es el pilar fundamental para nuestro análisis de datos. Ahora tenemos las herramientas para extraer datos de manera eficiente y segura desde nuestra base de datos.

# Análisis de Datos con Python + PostgreSQL

## Introducción al análisis integrado

En las secciones anteriores, configuramos nuestro entorno PostgreSQL con Docker y aprendimos a establecer conexiones desde Python. Ahora llegamos a la parte más emocionante: combinar estas herramientas para realizar análisis de datos poderosos.

Esta integración representa un flujo de trabajo completo: PostgreSQL nos proporciona un almacenamiento estructurado, consultas eficientes y capacidades de filtrado avanzadas, mientras que Python nos ofrece bibliotecas especializadas para procesamiento, análisis estadístico y visualización.

Piensen en PostgreSQL como una bodega organizada donde guardamos nuestros datos de manera eficiente, y en Python como el taller donde transformamos esos materiales en productos terminados con valor. La combinación de ambos nos permite manejar grandes volúmenes de datos con la flexibilidad analítica que necesitamos.

## Extracción de datos desde PostgreSQL a estructuras de Python

El primer paso en nuestro flujo de análisis es extraer los datos desde PostgreSQL a estructuras que Python pueda manejar eficientemente. La biblioteca estrella para este trabajo es pandas, que nos proporciona el DataFrame, una estructura bidimensional similar a una hoja de cálculo pero con capacidades analíticas avanzadas.

Comencemos instalando las bibliotecas necesarias:

```python
# En un entorno virtual o Jupyter Notebook
pip install pandas sqlalchemy psycopg2-binary matplotlib seaborn

```

Ahora, veamos diferentes formas de cargar datos desde PostgreSQL a un DataFrame de pandas:

### Método 1: Utilizando pandas.read_sql directamente

```python
import pandas as pd
import psycopg2

# Establecer conexión
conn = psycopg2.connect(
    host="localhost",
    port="5432",
    dbname="analisis_datos",
    user="postgres",
    password="micontraseña"
)

# Cargar datos directamente en un DataFrame
query = "SELECT * FROM ventas.productos"
df_productos = pd.read_sql(query, conn)

# Visualizar los datos cargados
print(df_productos.head())
print(df_productos.info())

# No olvidar cerrar la conexión
conn.close()

```

### Método 2: Usando SQLAlchemy como engine

```python
from sqlalchemy import create_engine
import pandas as pd

# Crear el engine de SQLAlchemy
engine = create_engine("postgresql://postgres:micontraseña@localhost:5432/analisis_datos")

# Cargar datos con el engine
df_clientes = pd.read_sql("SELECT * FROM ventas.clientes", engine)
print(df_clientes.head())

# Podemos ejecutar consultas más complejas
query = """
SELECT c.nombre as cliente, p.nombre as producto,
       t.cantidad, t.monto_total, t.fecha_transaccion
FROM ventas.transacciones t
JOIN ventas.clientes c ON t.id_cliente = c.id_cliente
JOIN ventas.productos p ON t.id_producto = p.id_producto
ORDER BY t.fecha_transaccion DESC
"""
df_transacciones = pd.read_sql(query, engine)
print(df_transacciones.head())

```

### Optimizando consultas para grandes volúmenes

Cuando trabajamos con tablas grandes, es importante optimizar nuestras consultas:

```python
# 1. Seleccionar solo las columnas necesarias
query = "SELECT id_producto, nombre, precio FROM ventas.productos"

# 2. Filtrar en la base de datos, no en Python
query = "SELECT * FROM ventas.productos WHERE precio > 100"

# 3. Usar chunking para tablas muy grandes
chunks = []
for chunk in pd.read_sql("SELECT * FROM tabla_grande", engine, chunksize=10000):
    # Procesar cada chunk
    processed = chunk.process_somehow()
    chunks.append(processed)

# Combinar los resultados
result = pd.concat(chunks)

# 4. Usar parámetros para consultas seguras
parametros = {'min_precio': 100, 'categoria': 'Electrónicos'}
query = "SELECT * FROM ventas.productos WHERE precio > %(min_precio)s AND categoria = %(categoria)s"
df = pd.read_sql(query, engine, params=parametros)

```

### Carga y manipulación eficiente con consultas avanzadas

PostgreSQL tiene funcionalidades analíticas que podemos aprovechar:

```python
# Aprovechar funciones de agregación de PostgreSQL
query = """
SELECT
    DATE_TRUNC('month', fecha_transaccion) as mes,
    COUNT(*) as num_transacciones,
    SUM(monto_total) as ingresos_totales,
    AVG(monto_total) as ticket_promedio
FROM ventas.transacciones
GROUP BY DATE_TRUNC('month', fecha_transaccion)
ORDER BY mes
"""
df_metricas_mensuales = pd.read_sql(query, engine)

```

Este enfoque delega el trabajo pesado a la base de datos, lo que suele ser más eficiente para operaciones de agregación sobre grandes volúmenes de datos.

## Procesamiento y limpieza de datos

Una vez que tenemos nuestros datos en DataFrames de pandas, podemos realizar todo tipo de operaciones de limpieza y transformación:

### Exploración inicial

```python
# Dimensiones del DataFrame
print(f"Filas: {df.shape[0]}, Columnas: {df.shape[1]}")

# Tipos de datos
print(df.dtypes)

# Estadísticas básicas
print(df.describe())

# Valores faltantes
print(df.isna().sum())

```

### Limpieza de datos

```python
# 1. Manejo de valores faltantes
# Rellenar con ceros
df['columna'].fillna(0, inplace=True)
# O eliminar filas con valores nulos
df.dropna(subset=['columna_importante'], inplace=True)

# 2. Eliminación de duplicados
df.drop_duplicates(inplace=True)

# 3. Corrección de tipos de datos
df['fecha'] = pd.to_datetime(df['fecha'])
df['precio'] = df['precio'].astype(float)

# 4. Normalización de texto
df['categoria'] = df['categoria'].str.lower().str.strip()

# 5. Tratamiento de valores atípicos (outliers)
# Método IQR (Rango Intercuartil)
Q1 = df['precio'].quantile(0.25)
Q3 = df['precio'].quantile(0.75)
IQR = Q3 - Q1
df_filtrado = df[(df['precio'] >= Q1 - 1.5 * IQR) & (df['precio'] <= Q3 + 1.5 * IQR)]

```

### Transformación y enriquecimiento

```python
# 1. Creación de nuevas columnas
df['margen'] = df['precio'] - df['costo']
df['margen_porcentual'] = (df['margen'] / df['precio']) * 100

# 2. Extracción de componentes de fechas
df['mes'] = df['fecha_transaccion'].dt.month
df['dia_semana'] = df['fecha_transaccion'].dt.day_name()

# 3. Categorización
df['rango_precio'] = pd.cut(df['precio'],
                           bins=[0, 50, 100, 500, float('inf')],
                           labels=['Bajo', 'Medio', 'Alto', 'Premium'])

# 4. One-hot encoding para variables categóricas
df_encoded = pd.get_dummies(df, columns=['categoria'])

```

### Agregaciones y análisis

```python
# 1. Agrupamiento por categoría
by_category = df.groupby('categoria').agg({
    'precio': ['mean', 'min', 'max', 'count'],
    'margen': ['mean', 'sum']
})

# 2. Ventas por mes
ventas_mes = df.groupby(df['fecha_transaccion'].dt.month)['monto_total'].sum()

# 3. Tablas pivote
tabla_pivot = pd.pivot_table(df,
                             values='monto_total',
                             index='cliente',
                             columns='mes',
                             aggfunc='sum',
                             fill_value=0)

# 4. Análisis de correlación
matriz_corr = df[['precio', 'cantidad', 'monto_total', 'margen']].corr()

```

## Visualización básica de datos

La visualización es crucial para comunicar los resultados de nuestros análisis. Python ofrece diversas bibliotecas para esto, siendo matplotlib y seaborn las más populares:

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Configuración para gráficos más estéticos
plt.style.use('seaborn-v0_8-whitegrid')
sns.set_palette("deep")

```

### Gráficos de líneas para datos temporales

```python
# Preparar datos
ventas_mensuales = df.groupby(df['fecha_transaccion'].dt.strftime('%Y-%m'))['monto_total'].sum().reset_index()
ventas_mensuales.columns = ['Mes', 'Ventas']

# Crear gráfico
plt.figure(figsize=(12, 6))
sns.lineplot(x='Mes', y='Ventas', data=ventas_mensuales, marker='o', linewidth=2)
plt.title('Evolución de Ventas Mensuales', fontsize=15)
plt.xlabel('Mes')
plt.ylabel('Ventas Totales ($)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

```

### Comparación entre categorías

```python
# Ventas por categoría
ventas_categoria = df.groupby('categoria')['monto_total'].sum().sort_values(ascending=False)

# Gráfico de barras
plt.figure(figsize=(10, 6))
sns.barplot(x=ventas_categoria.index, y=ventas_categoria.values)
plt.title('Ventas Totales por Categoría de Producto', fontsize=15)
plt.xlabel('Categoría')
plt.ylabel('Ventas Totales ($)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

```

### Distribuciones

```python
# Distribución de precios
plt.figure(figsize=(10, 6))
sns.histplot(df['precio'], bins=20, kde=True)
plt.title('Distribución de Precios de Productos', fontsize=15)
plt.xlabel('Precio ($)')
plt.ylabel('Frecuencia')
plt.tight_layout()
plt.show()

```

### Relaciones entre variables

```python
# Relación precio vs. cantidad vendida
plt.figure(figsize=(10, 6))
sns.scatterplot(x='precio', y='cantidad', data=df, hue='categoria', size='monto_total', sizes=(20, 200))
plt.title('Relación entre Precio y Cantidad Vendida', fontsize=15)
plt.xlabel('Precio ($)')
plt.ylabel('Cantidad Vendida')
plt.tight_layout()
plt.show()

```

### Heatmaps para correlaciones

```python
# Matriz de correlación
plt.figure(figsize=(8, 6))
sns.heatmap(matriz_corr, annot=True, cmap='coolwarm', vmin=-1, vmax=1)
plt.title('Correlación entre Variables', fontsize=15)
plt.tight_layout()
plt.show()

```

### Combinando PostgreSQL, pandas y visualización

Veamos un ejemplo completo que integra todo lo que hemos visto:

```python
import pandas as pd
from sqlalchemy import create_engine
import matplotlib.pyplot as plt
import seaborn as sns

# Conexión a la base de datos
engine = create_engine("postgresql://postgres:micontraseña@localhost:5432/analisis_datos")

# Consulta SQL avanzada para análisis de ventas por cliente/categoría
query = """
SELECT
    c.nombre as cliente,
    p.categoria,
    COUNT(t.id_transaccion) as num_compras,
    SUM(t.cantidad) as unidades_totales,
    SUM(t.monto_total) as monto_total,
    AVG(t.monto_total) as ticket_promedio,
    MIN(t.fecha_transaccion) as primera_compra,
    MAX(t.fecha_transaccion) as ultima_compra
FROM ventas.transacciones t
JOIN ventas.clientes c ON t.id_cliente = c.id_cliente
JOIN ventas.productos p ON t.id_producto = p.id_producto
GROUP BY c.nombre, p.categoria
ORDER BY monto_total DESC
"""

# Cargar datos en DataFrame
df_analisis = pd.read_sql(query, engine)

# Procesamiento y limpieza
df_analisis['dias_entre_compras'] = (pd.to_datetime(df_analisis['ultima_compra']) -
                                     pd.to_datetime(df_analisis['primera_compra'])).dt.days
df_analisis['compras_por_dia'] = df_analisis['num_compras'] / df_analisis['dias_entre_compras'].replace(0, 1)

# Crear visualizaciones
plt.figure(figsize=(12, 8))

# Subplot 1: Top clientes por monto total
plt.subplot(2, 2, 1)
top_clientes = df_analisis.groupby('cliente')['monto_total'].sum().sort_values(ascending=False).head(5)
sns.barplot(x=top_clientes.values, y=top_clientes.index)
plt.title('Top 5 Clientes por Ventas')
plt.xlabel('Ventas Totales ($)')

# Subplot 2: Distribución de ventas por categoría
plt.subplot(2, 2, 2)
cat_ventas = df_analisis.groupby('categoria')['monto_total'].sum()
plt.pie(cat_ventas, labels=cat_ventas.index, autopct='%1.1f%%', startangle=90)
plt.title('Distribución de Ventas por Categoría')

# Subplot 3: Relación entre frecuencia de compra y monto
plt.subplot(2, 1, 2)
sns.scatterplot(data=df_analisis, x='compras_por_dia', y='ticket_promedio',
                hue='categoria', size='unidades_totales', sizes=(20, 200))
plt.title('Relación entre Frecuencia de Compra y Ticket Promedio')
plt.xlabel('Compras por Día')
plt.ylabel('Ticket Promedio ($)')

plt.tight_layout()
plt.show()

# Guardar resultados del análisis en la base de datos
df_resultados = df_analisis[['cliente', 'categoria', 'monto_total', 'ticket_promedio']]
df_resultados.to_sql('resultados_analisis', engine, schema='ventas', if_exists='replace', index=False)

```

Este ejemplo completo muestra cómo podemos:

1. Extraer datos con una consulta SQL compleja
2. Procesar y transformar los datos en pandas
3. Crear visualizaciones informativas
4. Guardar los resultados de nuevo en la base de datos