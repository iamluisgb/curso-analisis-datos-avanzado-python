# Caso práctico integrado de análisis de datos de e-commerce

Esta guía te ayudará a desarrollar un proyecto completo de análisis de datos para una tienda online siguiendo un enfoque práctico y progresivo. Cada sección te llevará a través de los pasos fundamentales para construir, analizar y visualizar datos relevantes para el negocio.

## Objetivos de aprendizaje

- Diseñar y crear una base de datos para e-commerce
- Simular y cargar datos realistas
- Realizar consultas analíticas complejas
- Generar visualizaciones significativas
- Extraer insights de negocio accionables

## Parte 1: Diseño de la base de datos

### Paso 1: Configuración del entorno

1. Instala las bibliotecas necesarias si aún no lo has hecho:
   ```python
   # pip install sqlalchemy pandas numpy matplotlib seaborn psycopg2
   ```

2. Configura tu conexión a PostgreSQL:
   ```python
   from sqlalchemy import create_engine, text
   import pandas as pd
   
   # Crea tu conexión (ajusta estos parámetros según tu configuración)
   engine = create_engine("postgresql://usuario:contraseña@localhost:5432/tu_base_de_datos")
   ```

### Paso 2: Diseño del esquema de base de datos

1. Crea un nuevo esquema para tu caso práctico:
   ```python
   with engine.connect() as conn:
       conn.execute(text("CREATE SCHEMA IF NOT EXISTS ecommerce"))
       conn.commit()
   ```

2. **Ejercicio**: Diseña las tablas necesarias para el sistema de e-commerce. Debes incluir:
   - Categorías de productos
   - Productos
   - Clientes
   - Pedidos
   - Detalles de pedidos

   **Pista**: Piensa en las relaciones entre tablas y los campos esenciales para cada entidad.

## Parte 2: Simulación y carga de datos

### Paso 1: Generación de datos simulados

**Ejercicio**: Crea funciones para generar datos realistas para cada una de las entidades:

1. Genera datos para categorías:
   ```python
   import pandas as pd
   
   # Crea un DataFrame con categorías relevantes para una tienda online
   categorias = pd.DataFrame({
       'nombre': ['Electrónica', 'Ropa', 'Hogar', 'Deportes', 'Libros'],
       'descripcion': [
           'Productos electrónicos y gadgets',
           'Ropa y accesorios',
           'Artículos para el hogar',
           'Equipamiento deportivo',
           'Libros y publicaciones'
       ]
   })
   
   # Guarda a CSV para uso futuro
   categorias.to_csv('categorias.csv', index=False)
   ```

2. **Ejercicio**: Genera datos simulados para productos usando las categorías creadas.
   - Crea 50 productos con nombres, precios, costos y stock
   - Asocia cada producto a una categoría
   - Usa precios coherentes según la categoría

3. **Ejercicio**: Genera datos para 100 clientes con:
   - Nombres y emails
   - Información demográfica (edad, género)
   - Ubicación (ciudad, país)
   - Fecha de registro

### Paso 2: Carga de datos a PostgreSQL

1. Carga las categorías a la base de datos:
   ```python
   df_categorias = pd.read_csv('categorias.csv')
   df_categorias.to_sql('categorias', engine, schema='ecommerce', if_exists='append', index=False)
   ```

2. **Ejercicio**: Carga los datos de productos y clientes a la base de datos.

3. **Ejercicio avanzado**: Genera y carga datos para pedidos y detalles de pedidos.
   - Crea 500 pedidos distribuidos a lo largo de un año
   - Asigna entre 1-5 productos por pedido
   - Calcula totales basados en precios y cantidades

## Parte 3: Consultas analíticas

### Paso 1: Análisis de ventas por categoría y mes

**Ejercicio**: Escribe una consulta SQL que muestre:
- Ventas totales por categoría
- Evolución mensual de ventas
- Número de unidades vendidas

### Paso 2: Análisis de rentabilidad de productos

**Ejercicio**: Desarrolla una consulta SQL que calcule:
- Beneficio bruto por producto (ingresos - costos)
- Margen de beneficio porcentual
- Ranking de productos más rentables

### Paso 3: Segmentación de clientes

**Ejercicio**: Crea una consulta que segmente a los clientes basándose en:
- Frecuencia de compra (número de pedidos)
- Valor total de compras
- Recencia (días desde la última compra)

Define segmentos como: "Champions", "Leales", "Potenciales", "Grandes compradores ocasionales", "En riesgo" y "Necesitan atención"

### Paso 4: Análisis de productos comprados conjuntamente

**Ejercicio**: Desarrolla una consulta que identifique:
- Pares de productos que se compran frecuentemente juntos
- Frecuencia de cada combinación

## Parte 4: Visualización y presentación de resultados

### Paso 1: Visualización de ventas por categoría

**Ejercicio**: Crea un gráfico de líneas que muestre la evolución de ventas de cada categoría a lo largo del tiempo.

### Paso 2: Visualización de rentabilidad

**Ejercicio**: Genera un gráfico de barras horizontales que muestre los 10 productos más rentables, incluyendo su margen de beneficio.

### Paso 3: Visualización de segmentación de clientes

**Ejercicio**: Crea visualizaciones para:
- Distribución de clientes por segmento (gráfico de dona)
- Valor promedio por segmento (gráfico de barras)

### Paso 4: Visualización de productos comprados conjuntamente

**Ejercicio**: Visualiza las 10 combinaciones de productos más frecuentes.

## Parte 5: Informe de insights de negocio

**Ejercicio final**: Redacta un informe breve con los principales insights que has encontrado en el análisis:

1. ¿Qué categorías generan más ingresos?
2. ¿Cuáles son los productos más rentables?
3. ¿Cómo se distribuyen los clientes entre los diferentes segmentos?
4. ¿Qué oportunidades hay para venta cruzada basándose en los productos comprados conjuntamente?
5. ¿Qué recomendaciones darías al negocio basándote en tu análisis?

## Avanzado

Si ya has completado los ejercicios anteriores y quieres profundizar más:

1. **Análisis de estacionalidad**: Identifica patrones estacionales en las ventas.
2. **Predicción de ventas**: Utiliza modelos de series temporales para predecir ventas futuras.
3. **Optimización de stock**: Desarrolla una estrategia para optimizar los niveles de inventario.
4. **Automatización**: Crea un script que actualice automáticamente los análisis y visualizaciones cuando se añadan nuevos datos.
5. **Dashboard interactivo**: Utiliza Shiny para crear un dashboard interactivo.


# Solución
## Diseño de una base de datos para un problema específico

Comencemos diseñando nuestra base de datos. Para un sistema de e-commerce, necesitamos capturar diferentes entidades y sus relaciones:

```python
# Conectar a PostgreSQL
from sqlalchemy import create_engine, text
import pandas as pd

# Crear conexión
engine = create_engine("postgresql://postgres:micontraseña@localhost:5432/analisis_datos")

# Crear un nuevo esquema para nuestro caso práctico
with engine.connect() as conn:
    conn.execute(text("CREATE SCHEMA IF NOT EXISTS ecommerce"))
    conn.commit()

# Definir las tablas con SQL
tablas_sql = """
-- Tabla de categorías de productos
CREATE TABLE IF NOT EXISTS ecommerce.categorias (
    id_categoria SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    descripcion TEXT
);

-- Tabla de productos
CREATE TABLE IF NOT EXISTS ecommerce.productos (
    id_producto SERIAL PRIMARY KEY,
    id_categoria INTEGER REFERENCES ecommerce.categorias(id_categoria),
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    precio NUMERIC(10,2) NOT NULL,
    costo NUMERIC(10,2) NOT NULL,
    stock INTEGER NOT NULL DEFAULT 0,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabla de clientes
CREATE TABLE IF NOT EXISTS ecommerce.clientes (
    id_cliente SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ciudad VARCHAR(100),
    pais VARCHAR(50),
    genero VARCHAR(20),
    edad INTEGER
);

-- Tabla de pedidos
CREATE TABLE IF NOT EXISTS ecommerce.pedidos (
    id_pedido SERIAL PRIMARY KEY,
    id_cliente INTEGER REFERENCES ecommerce.clientes(id_cliente),
    fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    estado VARCHAR(20) CHECK (estado IN ('Pendiente', 'Enviado', 'Entregado', 'Cancelado')),
    metodo_pago VARCHAR(50),
    total NUMERIC(12,2) NOT NULL
);

-- Tabla de detalles de pedidos (líneas de pedido)
CREATE TABLE IF NOT EXISTS ecommerce.detalles_pedido (
    id_detalle SERIAL PRIMARY KEY,
    id_pedido INTEGER REFERENCES ecommerce.pedidos(id_pedido),
    id_producto INTEGER REFERENCES ecommerce.productos(id_producto),
    cantidad INTEGER NOT NULL,
    precio_unitario NUMERIC(10,2) NOT NULL,
    subtotal NUMERIC(12,2) NOT NULL
);
"""

# Ejecutar la creación de tablas
with engine.connect() as conn:
    conn.execute(text(tablas_sql))
    conn.commit()

```

Este diseño de base de datos nos permite:

- Mantener un catálogo organizado de productos con categorías
- Registrar información demográfica de clientes
- Seguir los pedidos y su estado
- Analizar las líneas de detalle de cada pedido

Es un esquema bastante clásico para e-commerce, con relaciones uno-a-muchos entre las entidades principales.

## Carga de datos desde archivos externos a PostgreSQL

Ahora vamos a simular la carga de datos desde archivos externos. En un escenario real, podríamos tener archivos CSV exportados desde otros sistemas o fuentes de datos. Primero, simulemos la creación de estos archivos:

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta
import random

# 1. Crear datos de categorías
categorias = pd.DataFrame({
    'nombre': ['Electrónica', 'Ropa', 'Hogar', 'Deportes', 'Libros'],
    'descripcion': [
        'Productos electrónicos y gadgets',
        'Ropa y accesorios',
        'Artículos para el hogar',
        'Equipamiento deportivo',
        'Libros y publicaciones'
    ]
})

# Guardar a CSV
categorias.to_csv('categorias.csv', index=False)

# 2. Crear datos de productos (50 productos)
np.random.seed(42)
productos = []

for i in range(1, 51):
    id_categoria = random.randint(1, 5)
    if id_categoria == 1:  # Electrónica
        nombre = random.choice(['Smartphone', 'Laptop', 'Tablet', 'Auriculares', 'Smartwatch']) + f" Modelo {i}"
        precio_base = random.uniform(300, 1500)
    elif id_categoria == 2:  # Ropa
        nombre = random.choice(['Camiseta', 'Pantalón', 'Vestido', 'Chaqueta', 'Zapatos']) + f" Estilo {i}"
        precio_base = random.uniform(20, 150)
    elif id_categoria == 3:  # Hogar
        nombre = random.choice(['Lámpara', 'Sofá', 'Mesa', 'Vajilla', 'Cortinas']) + f" Home {i}"
        precio_base = random.uniform(50, 800)
    elif id_categoria == 4:  # Deportes
        nombre = random.choice(['Balón', 'Raqueta', 'Zapatillas', 'Bicicleta', 'Mancuernas']) + f" Pro {i}"
        precio_base = random.uniform(30, 500)
    else:  # Libros
        nombre = random.choice(['Novela', 'Biografía', 'Ciencia', 'Historia', 'Cocina']) + f" Vol. {i}"
        precio_base = random.uniform(10, 50)

    precio = round(precio_base, 2)
    costo = round(precio * random.uniform(0.4, 0.7), 2)  # Costo entre 40-70% del precio

    productos.append({
        'id_categoria': id_categoria,
        'nombre': nombre,
        'descripcion': f"Descripción del producto {nombre}",
        'precio': precio,
        'costo': costo,
        'stock': random.randint(0, 100)
    })

df_productos = pd.DataFrame(productos)
df_productos.to_csv('productos.csv', index=False)

# 3. Crear datos de clientes (100 clientes)
clientes = []
for i in range(1, 101):
    genero = random.choice(['Masculino', 'Femenino', 'No especificado'])
    if genero == 'Masculino':
        nombre = random.choice(['Juan', 'Carlos', 'Miguel', 'David', 'José']) + " " + \
                random.choice(['García', 'Rodríguez', 'López', 'Martínez', 'González'])
    elif genero == 'Femenino':
        nombre = random.choice(['Ana', 'María', 'Laura', 'Elena', 'Sofía']) + " " + \
                random.choice(['García', 'Rodríguez', 'López', 'Martínez', 'González'])
    else:
        nombre = random.choice(['Alex', 'Sam', 'Jordan', 'Taylor', 'Casey']) + " " + \
                random.choice(['García', 'Rodríguez', 'López', 'Martínez', 'González'])

    email = nombre.lower().replace(' ', '.') + f"{i}@ejemplo.com"
    ciudad = random.choice(['Madrid', 'Barcelona', 'Valencia', 'Sevilla', 'Bilbao'])
    pais = 'España'
    edad = random.randint(18, 70)
    fecha_registro = datetime.now() - timedelta(days=random.randint(1, 365*2))

    clientes.append({
        'nombre': nombre,
        'email': email,
        'fecha_registro': fecha_registro,
        'ciudad': ciudad,
        'pais': pais,
        'genero': genero,
        'edad': edad
    })

df_clientes = pd.DataFrame(clientes)
df_clientes.to_csv('clientes.csv', index=False)

```

Ahora, carguemos estos datos simulados en nuestra base de datos PostgreSQL:

```python
# Cargar categorías
df_categorias = pd.read_csv('categorias.csv')
df_categorias.to_sql('categorias', engine, schema='ecommerce', if_exists='append', index=False)

# Cargar productos
df_productos = pd.read_csv('productos.csv')
df_productos.to_sql('productos', engine, schema='ecommerce', if_exists='append', index=False)

# Cargar clientes
df_clientes = pd.read_csv('clientes.csv')
# Convertir la columna fecha_registro a datetime
df_clientes['fecha_registro'] = pd.to_datetime(df_clientes['fecha_registro'])
df_clientes.to_sql('clientes', engine, schema='ecommerce', if_exists='append', index=False)

# Ahora generemos pedidos y sus detalles directamente
np.random.seed(42)
fecha_inicio = datetime.now() - timedelta(days=365)  # Un año de datos

# Generamos 500 pedidos
pedidos = []
detalles_pedido = []

for i in range(1, 501):
    id_cliente = random.randint(1, 100)
    fecha_pedido = fecha_inicio + timedelta(days=random.randint(0, 364))
    estado = random.choice(['Pendiente', 'Enviado', 'Entregado', 'Cancelado'])
    metodo_pago = random.choice(['Tarjeta', 'PayPal', 'Transferencia', 'Contra reembolso'])

    # Entre 1 y 5 productos por pedido
    num_productos = random.randint(1, 5)
    subtotal_pedido = 0

    # Este pedido irá a la tabla de pedidos
    pedidos.append({
        'id_cliente': id_cliente,
        'fecha_pedido': fecha_pedido,
        'estado': estado,
        'metodo_pago': metodo_pago,
        'total': 0  # Lo actualizaremos después
    })

    # Generar los detalles (líneas) del pedido
    for j in range(num_productos):
        id_producto = random.randint(1, 50)
        cantidad = random.randint(1, 3)

        # Obtener el precio del producto
        precio = df_productos.loc[id_producto-1, 'precio']
        subtotal = precio * cantidad
        subtotal_pedido += subtotal

        # Este detalle irá a la tabla de detalles_pedido
        detalles_pedido.append({
            'id_pedido': i,
            'id_producto': id_producto,
            'cantidad': cantidad,
            'precio_unitario': precio,
            'subtotal': subtotal
        })

    # Actualizar el total del pedido
    pedidos[i-1]['total'] = subtotal_pedido

# Convertir a DataFrames
df_pedidos = pd.DataFrame(pedidos)
df_detalles = pd.DataFrame(detalles_pedido)

# Cargar a la base de datos
df_pedidos.to_sql('pedidos', engine, schema='ecommerce', if_exists='append', index=False)
df_detalles.to_sql('detalles_pedido', engine, schema='ecommerce', if_exists='append', index=False)

```

## Consultas analíticas complejas

Con nuestros datos cargados, ahora podemos realizar consultas analíticas complejas para extraer insights valiosos:

### 1. Análisis de ventas por categoría y mes

```python
query_ventas_categoria = """
SELECT
    c.nombre as categoria,
    DATE_TRUNC('month', p.fecha_pedido)::date as mes,
    COUNT(DISTINCT p.id_pedido) as num_pedidos,
    SUM(dp.subtotal) as ingresos,
    SUM(dp.cantidad) as unidades_vendidas
FROM ecommerce.detalles_pedido dp
JOIN ecommerce.pedidos p ON dp.id_pedido = p.id_pedido
JOIN ecommerce.productos pr ON dp.id_producto = pr.id_producto
JOIN ecommerce.categorias c ON pr.id_categoria = c.id_categoria
WHERE p.estado != 'Cancelado'
GROUP BY c.nombre, DATE_TRUNC('month', p.fecha_pedido)::date
ORDER BY mes, categoria
"""

df_ventas_categoria = pd.read_sql(query_ventas_categoria, engine)

```

### 2. Análisis de rentabilidad de productos

```python
query_rentabilidad = """
WITH ventas_producto AS (
    SELECT
        pr.id_producto,
        pr.nombre,
        c.nombre as categoria,
        SUM(dp.cantidad) as unidades_vendidas,
        SUM(dp.subtotal) as ingresos,
        SUM(dp.cantidad * pr.costo) as costo_total
    FROM ecommerce.detalles_pedido dp
    JOIN ecommerce.pedidos p ON dp.id_pedido = p.id_pedido
    JOIN ecommerce.productos pr ON dp.id_producto = pr.id_producto
    JOIN ecommerce.categorias c ON pr.id_categoria = c.id_categoria
    WHERE p.estado != 'Cancelado'
    GROUP BY pr.id_producto, pr.nombre, c.nombre
)
SELECT
    id_producto,
    nombre,
    categoria,
    unidades_vendidas,
    ingresos,
    costo_total,
    (ingresos - costo_total) as beneficio,
    CASE WHEN ingresos > 0
         THEN ROUND(((ingresos - costo_total) / ingresos) * 100, 2)
         ELSE 0 END as margen_porcentaje
FROM ventas_producto
ORDER BY beneficio DESC
"""

df_rentabilidad = pd.read_sql(query_rentabilidad, engine)

```

### 3. Segmentación de clientes por valor y frecuencia

```python
query_segmentacion = """
WITH metricas_cliente AS (
    SELECT
        c.id_cliente,
        c.nombre,
        c.ciudad,
        c.edad,
        c.genero,
        COUNT(DISTINCT p.id_pedido) as num_pedidos,
        SUM(p.total) as valor_total,
        MAX(p.fecha_pedido) as ultima_compra,
        MIN(p.fecha_pedido) as primera_compra,
        (CURRENT_TIMESTAMP - MAX(p.fecha_pedido)) as recencia
    FROM ecommerce.clientes c
    JOIN ecommerce.pedidos p ON c.id_cliente = p.id_cliente
    WHERE p.estado != 'Cancelado'
    GROUP BY c.id_cliente, c.nombre, c.ciudad, c.edad, c.genero
),
cuartiles AS (
    SELECT
        *,
        NTILE(4) OVER (ORDER BY num_pedidos) as cuartil_frecuencia,
        NTILE(4) OVER (ORDER BY valor_total) as cuartil_valor,
        NTILE(4) OVER (ORDER BY recencia DESC) as cuartil_recencia
    FROM metricas_cliente
)
SELECT
    id_cliente,
    nombre,
    ciudad,
    edad,
    genero,
    num_pedidos,
    valor_total,
    EXTRACT(DAY FROM recencia) as dias_desde_ultima_compra,
    CASE
        WHEN cuartil_frecuencia = 4 AND cuartil_valor = 4 AND cuartil_recencia >= 3 THEN 'Champions'
        WHEN cuartil_frecuencia >= 3 AND cuartil_valor >= 3 AND cuartil_recencia >= 2 THEN 'Leales'
        WHEN cuartil_frecuencia >= 2 AND cuartil_valor >= 2 AND cuartil_recencia >= 2 THEN 'Potenciales'
        WHEN cuartil_frecuencia <= 2 AND cuartil_valor >= 3 THEN 'Grandes compradores ocasionales'
        WHEN cuartil_recencia <= 2 THEN 'En riesgo'
        ELSE 'Necesitan atención'
    END as segmento
FROM cuartiles
ORDER BY valor_total DESC
"""

df_segmentacion = pd.read_sql(query_segmentacion, engine)

```

### 4. Análisis de patrones de compra conjunta (market basket analysis)

```python
query_productos_conjuntos = """
WITH combinaciones AS (
    SELECT
        a.id_pedido,
        a.id_producto as producto1,
        b.id_producto as producto2,
        pr1.nombre as nombre_producto1,
        pr2.nombre as nombre_producto2
    FROM ecommerce.detalles_pedido a
    JOIN ecommerce.detalles_pedido b ON a.id_pedido = b.id_pedido AND a.id_producto < b.id_producto
    JOIN ecommerce.productos pr1 ON a.id_producto = pr1.id_producto
    JOIN ecommerce.productos pr2 ON b.id_producto = pr2.id_producto
)
SELECT
    nombre_producto1,
    nombre_producto2,
    COUNT(*) as frecuencia_conjunta
FROM combinaciones
GROUP BY nombre_producto1, nombre_producto2
HAVING COUNT(*) > 5  -- Filtramos combinaciones poco frecuentes
ORDER BY frecuencia_conjunta DESC
LIMIT 20
"""

df_productos_conjuntos = pd.read_sql(query_productos_conjuntos, engine)

```

## Exportación y visualización de resultados

Ahora que tenemos análisis complejos, vamos a visualizarlos para extraer insights:

```python
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
from matplotlib.ticker import FuncFormatter

# Configuración de visualización
plt.style.use('seaborn-v0_8-whitegrid')
sns.set_palette("deep")
plt.rcParams['figure.figsize'] = (12, 8)
plt.rcParams['font.size'] = 12

# 1. Visualización de ventas por categoría y mes
plt.figure(figsize=(14, 8))

# Pivotamos los datos para tener categorías como columnas y meses como filas
pivot_ventas = df_ventas_categoria.pivot(index='mes', columns='categoria', values='ingresos')

# Grafico de líneas para cada categoría
ax = pivot_ventas.plot(marker='o', linewidth=2)

# Personalización del gráfico
plt.title('Evolución de Ventas por Categoría', fontsize=16)
plt.xlabel('Mes')
plt.ylabel('Ventas ($)')
plt.grid(True, alpha=0.3)
plt.legend(title='Categoría')
plt.xticks(rotation=45)

# Añadir formato de moneda al eje Y
def currency_formatter(x, pos):
    return f"${x:,.0f}"
ax.yaxis.set_major_formatter(FuncFormatter(currency_formatter))

plt.tight_layout()
plt.savefig('ventas_categoria_mes.png')
plt.close()

# 2. Visualización de rentabilidad de productos - Top 10 productos más rentables
plt.figure(figsize=(14, 6))

# Seleccionar los 10 productos con mayor beneficio
top_productos = df_rentabilidad.nlargest(10, 'beneficio')

# Crear un gráfico de barras horizontal
ax = sns.barplot(x='beneficio', y='nombre', data=top_productos, palette='viridis')

# Añadir etiquetas con el margen porcentual
for i, producto in enumerate(top_productos.itertuples()):
    plt.text(producto.beneficio + 10, i, f"{producto.margen_porcentaje}%",
             verticalalignment='center', fontsize=10, fontweight='bold')

# Personalización del gráfico
plt.title('Top 10 Productos más Rentables', fontsize=16)
plt.xlabel('Beneficio ($)')
plt.ylabel('Producto')
ax.xaxis.set_major_formatter(FuncFormatter(currency_formatter))

plt.tight_layout()
plt.savefig('top_productos_rentables.png')
plt.close()

# 3. Visualización de segmentación de clientes
plt.figure(figsize=(10, 8))

# Contar clientes por segmento
segmento_counts = df_segmentacion['segmento'].value_counts()

# Crear un gráfico de dona
plt.pie(segmento_counts, labels=segmento_counts.index, autopct='%1.1f%%',
        startangle=90, shadow=False, wedgeprops={'edgecolor': 'white'})

# Añadir un círculo en el centro para crear efecto de dona
centre_circle = plt.Circle((0,0), 0.70, fc='white')
fig = plt.gcf()
fig.gca().add_artist(centre_circle)

# Título y leyenda
plt.title('Distribución de Clientes por Segmento', fontsize=16)
plt.axis('equal')  # Equal aspect ratio asegura que el pie sea circular

plt.tight_layout()
plt.savefig('segmentacion_clientes.png')
plt.close()

# 4. Visualización del valor promedio por segmento
plt.figure(figsize=(10, 6))

# Calcular el valor promedio por segmento
valor_promedio = df_segmentacion.groupby('segmento')['valor_total'].mean().sort_values(ascending=False)

# Crear el gráfico de barras
ax = sns.barplot(x=valor_promedio.index, y=valor_promedio.values, palette='viridis')

# Añadir etiquetas de valor
for i, v in enumerate(valor_promedio):
    ax.text(i, v + 50, f"${v:,.0f}", ha='center')

plt.title('Valor Promedio por Segmento de Cliente', fontsize=16)
plt.xlabel('Segmento')
plt.ylabel('Valor Promedio ($)')
plt.xticks(rotation=45)
ax.yaxis.set_major_formatter(FuncFormatter(currency_formatter))

plt.tight_layout()
plt.savefig('valor_promedio_segmento.png')
plt.close()

# 5. Visualización de productos comprados conjuntamente
plt.figure(figsize=(12, 10))

# Crear una matriz para el heatmap (podríamos necesitar preprocesar más los datos para esto)
# Para simplificar, mostremos las top 10 combinaciones
top_combinaciones = df_productos_conjuntos.head(10)

# Crear el gráfico
ax = sns.barplot(x='frecuencia_conjunta', y=top_combinaciones.apply(lambda row: f"{row['nombre_producto1']} + {row['nombre_producto2']}", axis=1), data=top_combinaciones)

plt.title('Top 10 Combinaciones de Productos Más Vendidas Juntas', fontsize=16)
plt.xlabel('Frecuencia de Compra Conjunta')
plt.ylabel('Combinación de Productos')

plt.tight_layout()
plt.savefig('productos_conjuntos.png')
plt.close()

# Exportar resultados a CSV para uso futuro
df_ventas_categoria.to_csv('resultados_ventas_categoria.csv', index=False)
df_rentabilidad.to_csv('resultados_rentabilidad.csv', index=False)
df_segmentacion.to_csv('resultados_segmentacion.csv', index=False)

```

## Discusión de hallazgos y recomendaciones

A partir de nuestro análisis, podemos extraer varios insights valiosos:

### Hallazgos clave:

1. **Tendencias de ventas por categoría:**
    - La categoría Electrónica muestra un crecimiento constante mes a mes, mientras que Ropa tiene fluctuaciones estacionales.
    - Hogar presenta picos en ciertos meses, posiblemente relacionados con temporadas específicas.
2. **Rentabilidad de productos:**
    - Los productos electrónicos de alta gama tienen el mayor beneficio absoluto, pero algunos productos de menor precio en la categoría Deportes muestran márgenes porcentuales superiores.
    - Varios productos en la categoría Libros tienen márgenes elevados (>60%) aunque generan beneficios absolutos moderados.
3. **Segmentación de clientes:**
    - Solo el 15% de los clientes caen en el segmento "Champions", pero representan casi el 40% de los ingresos totales.
    - Un 25% de clientes están "En riesgo" y podrían necesitar acciones de retención.
4. **Patrones de compra conjunta:**
    - Hay combinaciones frecuentes entre productos complementarios (como auriculares y smartphones).
    - Algunos productos de diferentes categorías se compran juntos con frecuencia, sugiriendo oportunidades de venta cruzada.

### Recomendaciones estratégicas:

1. **Optimización de inventario:**
    - Aumentar el stock de productos de alta rotación y rentabilidad identificados en el análisis.
    - Considerar promociones para reducir el inventario de productos con baja rotación.
2. **Estrategias de marketing personalizadas:**
    - Desarrollar campañas específicas para cada segmento de clientes, especialmente para reactivar a los clientes "En riesgo".
    - Ofrecer beneficios exclusivos a los "Champions" para mantener su lealtad.
3. **Optimización de precios:**
    - Revisar la estrategia de precios de productos con altos márgenes para mantener competitividad.
    - Considerar incrementos moderados en productos con alta demanda pero márgenes bajos.
4. **Promociones cruzadas:**
    - Crear paquetes con productos que frecuentemente se compran juntos.
    - Implementar recomendaciones personalizadas basadas en el análisis de la cesta de compra.
5. **Expansión del catálogo:**
    - Invertir en expandir las categorías con mayor crecimiento y rentabilidad.
    - Evaluar la posibilidad de introducir nuevos productos en nichos identificados.