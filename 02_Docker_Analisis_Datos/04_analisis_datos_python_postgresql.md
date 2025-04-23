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