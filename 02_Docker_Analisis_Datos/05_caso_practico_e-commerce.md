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
