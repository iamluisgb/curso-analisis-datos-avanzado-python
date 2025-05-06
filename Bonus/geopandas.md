# Introducción a GeoPandas

Este tutorial introductorio presenta los conceptos clave y características básicas de GeoPandas para ayudarte a comenzar con tus proyectos de análisis geoespacial. Basado en: https://geopandas.org/en/stable/getting_started/introduction.html

## Conceptos fundamentales

GeoPandas, como su nombre sugiere, extiende la popular biblioteca de ciencia de datos pandas añadiendo soporte para datos geoespaciales.

La estructura de datos principal en GeoPandas es el `geopandas.GeoDataFrame`, una subclase de `pandas.DataFrame`, que puede almacenar columnas de geometría y realizar operaciones espaciales. El `geopandas.GeoSeries`, una subclase de `pandas.Series`, maneja las geometrías. Por lo tanto, tu GeoDataFrame es una combinación de `pandas.Series`, con datos tradicionales (numéricos, booleanos, texto, etc.), y `geopandas.GeoSeries`, con geometrías (puntos, polígonos, etc.). Puedes tener tantas columnas con geometrías como desees, a diferencia de algunos programas GIS de escritorio típicos.

Cada GeoSeries puede contener cualquier tipo de geometría (incluso puedes mezclarlas dentro de un solo array) y tiene un atributo `GeoSeries.crs`, que almacena información sobre la proyección (CRS significa Sistema de Referencia de Coordenadas). Por lo tanto, cada GeoSeries en un GeoDataFrame puede estar en una proyección diferente, permitiéndote tener, por ejemplo, múltiples versiones (diferentes proyecciones) de la misma geometría.

Solo una GeoSeries en un GeoDataFrame se considera la columna de geometría activa, lo que significa que todas las operaciones geométricas aplicadas a un GeoDataFrame operan sobre esta columna. Se accede a la columna de geometría activa a través del atributo `GeoDataFrame.geometry`.

## Lectura y escritura de archivos

Primero, necesitamos leer algunos datos.

### Leyendo archivos

Suponiendo que tienes un archivo que contiene tanto datos como geometría (p. ej., GeoPackage, GeoJSON, Shapefile), puedes leerlo usando `geopandas.read_file()`, que detecta automáticamente el tipo de archivo y crea un GeoDataFrame. Este tutorial utiliza el conjunto de datos "nybb", un mapa de los distritos de Nueva York, que está disponible a través del paquete geodatasets. Por lo tanto, usamos `geodatasets.get_path()` para descargar el conjunto de datos y obtener la ruta a la copia local.

```python
import geopandas
from geodatasets import get_path

# Obtener ruta al conjunto de datos
path_to_data = get_path("nybb")
# Leer el archivo en un GeoDataFrame
gdf = geopandas.read_file(path_to_data)

# Mostrar el GeoDataFrame
gdf

```

El resultado mostrará un GeoDataFrame con columnas como 'BoroCode', 'BoroName', 'Shape_Leng', 'Shape_Area' y 'geometry'. La columna 'geometry' contendrá objetos tipo MULTIPOLYGON que representan cada distrito.

### Escribiendo archivos

Para escribir un GeoDataFrame en un archivo, usa `GeoDataFrame.to_file()`. Por defecto, el formato del archivo se infiere por la extensión del archivo (p. ej., .shp para Shapefile, .geojson para GeoJSON), pero puedes especificarlo explícitamente con la palabra clave driver.

```python
# Guardar el GeoDataFrame como un archivo GeoJSON
gdf.to_file("mi_archivo.geojson", driver="GeoJSON")

```

## Accesores y métodos simples

Ahora tenemos nuestro GeoDataFrame y podemos comenzar a trabajar con su geometría.

Como solo había una columna de geometría en el conjunto de datos de los distritos de Nueva York, esta columna automáticamente se convierte en la geometría activa y los métodos espaciales utilizados en el GeoDataFrame se aplicarán a la columna "geometry".

### Midiendo el área

Para medir el área de cada polígono (o MultiPolygon en este caso específico), accede al atributo `GeoDataFrame.area`, que devuelve una `pandas.Series`. Ten en cuenta que `GeoDataFrame.area` es solo `GeoSeries.area` aplicado a la columna de geometría activa.

Pero primero, para facilitar la lectura de los resultados, establecemos los nombres de los distritos como el índice:

```python
# Establecer 'BoroName' como índice
gdf = gdf.set_index("BoroName")
# Calcular el área de cada distrito
gdf["area"] = gdf.area
# Mostrar las áreas
gdf["area"]

```

El resultado mostrará el área de cada distrito en las unidades del sistema de coordenadas del conjunto de datos (en este caso, pies cuadrados).

### Obteniendo el perímetro y el centroide del polígono

Para obtener el perímetro de cada polígono (LineString), accede a `GeoDataFrame.boundary`:

```python
# Calcular el perímetro de cada polígono
gdf["boundary"] = gdf.boundary
# Mostrar los límites
gdf["boundary"]

```

Como hemos guardado el er como una nueva columna, ahora tenemos dos columnas de geometría en el mismo GeoDataFrame.

También podemos crear nuevas geometrías, que podrían ser, por ejemplo, una versión con buffer del original (es decir, `GeoDataFrame.buffer(10)`) o su centroide:

```python
# Calcular el centroide de cada polígono
gdf["centroid"] = gdf.centroid
# Mostrar los centroides
gdf["centroid"]

```

### Midiendo distancias

También podemos medir qué tan lejos está cada centroide del primer centroide.

```python
# Obtener el primer punto (centroide)
first_point = gdf["centroid"].iloc[0]
# Calcular la distancia desde cada centroide hasta el primer centroide
gdf["distance"] = gdf["centroid"].distance(first_point)
# Mostrar las distancias
gdf["distance"]

```

Ten en cuenta que `geopandas.GeoDataFrame` es una subclase de `pandas.DataFrame`, por lo que tenemos toda la funcionalidad de pandas disponible para usar en el conjunto de datos geoespacial — incluso podemos realizar manipulaciones de datos con los atributos y la información de geometría juntos.

Por ejemplo, para calcular el promedio de las distancias medidas anteriormente, accede a la columna 'distance' y llama al método `mean()` en ella:

```python
# Calcular la distancia promedio
gdf["distance"].mean()

```

## Creando mapas

GeoPandas también puede graficar mapas, por lo que podemos verificar cómo aparecen las geometrías en el espacio. Para graficar la geometría activa, llama a `GeoDataFrame.plot()`. Para codificar por color según otra columna, pasa esa columna como el primer argumento. En el ejemplo a continuación, graficamos la columna de geometría activa y codificamos por color según la columna "area". También queremos mostrar una leyenda (`legend=True`).

```python
# Crear un mapa coloreado por área
gdf.plot("area", legend=True)

```

También puedes explorar tus datos de manera interactiva usando `GeoDataFrame.explore()`, que se comporta de la misma manera que `plot()` pero devuelve un mapa interactivo en su lugar.

```python
# Crear un mapa interactivo coloreado por área
gdf.explore("area", legend=False)

```

Cambiando la geometría activa (`GeoDataFrame.set_geometry`) a centroides, podemos graficar los mismos datos usando geometría de puntos.

```python
# Cambiar la geometría activa a centroides
gdf = gdf.set_geometry("centroid")
# Crear un mapa con los centroides coloreados por área
gdf.plot("area", legend=True)

```

Y también podemos superponer ambas GeoSeries. Solo necesitamos usar un gráfico como eje para el otro.

```python
# Crear un mapa con la geometría activa (centroides)
ax = gdf["geometry"].plot()
# Superponer los centroides en negro
gdf["centroid"].plot(ax=ax, color="black")

```

Ahora establecemos la geometría activa de vuelta a la GeoSeries original.

```python
# Volver a la geometría original
gdf = gdf.set_geometry("geometry")

```

## Creación de geometría

Podemos trabajar más con la geometría y crear nuevas formas basadas en las que ya tenemos.

### Casco convexo

Si estamos interesados en el casco convexo (convex hull) de nuestros polígonos, podemos acceder a `GeoDataFrame.convex_hull`.

```python
# Calcular el casco convexo de cada geometría
gdf["convex_hull"] = gdf.convex_hull
# Guardar el primer gráfico como un eje y establecer la transparencia (alpha) a 0.5
ax = gdf["convex_hull"].plot(alpha=0.5)
# Pasar el primer gráfico y establecer el ancho de línea a 0.5
gdf["boundary"].plot(ax=ax, color="white", linewidth=0.5)

```

### Buffer

En otros casos, podemos necesitar crear un buffer alrededor de la geometría usando `GeoDataFrame.buffer()`. Los métodos de geometría se aplican automáticamente a la geometría activa, pero también podemos aplicarlos directamente a cualquier GeoSeries. Creemos buffers para los distritos y sus centroides y grafiquemos ambos uno encima del otro.

```python
# Crear un buffer de 10,000 pies alrededor de cada distrito
# (la geometría ya está en pies)
gdf["buffered"] = gdf.buffer(10000)

# Crear un buffer de 10,000 pies alrededor de cada centroide
gdf["buffered_centroid"] = gdf["centroid"].buffer(10000)

# Guardar el primer gráfico como un eje y establecer la transparencia a 0.5
ax = gdf["buffered"].plot(alpha=0.5)
# Pasar el primer gráfico como eje al segundo
gdf["buffered_centroid"].plot(ax=ax, color="red", alpha=0.5)
# Pasar el primer gráfico y establecer el ancho de línea a 0.5
gdf["boundary"].plot(ax=ax, color="white", linewidth=0.5)

```

## Relaciones geométricas

También podemos preguntar sobre las relaciones espaciales de diferentes geometrías. Usando las geometrías anteriores, podemos verificar cuáles de los distritos con buffer intersectan la geometría original de Brooklyn, es decir, están a menos de 10,000 pies de Brooklyn.

Primero, obtenemos un polígono de Brooklyn.

```python
# Obtener la geometría de Brooklyn
brooklyn = gdf.loc["Brooklyn", "geometry"]
brooklyn

```

El polígono es un objeto de geometría shapely, como cualquier otra geometría utilizada en GeoPandas.

```python
# Verificar el tipo de la geometría
type(brooklyn)
# shapely.geometry.multipolygon.MultiPolygon

```

Luego podemos verificar cuáles de las geometrías en `gdf["buffered"]` intersectan con Brooklyn.

```python
# Verificar qué distritos con buffer intersectan con Brooklyn
gdf["buffered"].intersects(brooklyn)

```

Solo Bronx (en el norte) está a más de 10,000 pies de distancia de Brooklyn. Todos los demás están más cerca e intersectan nuestro polígono.

Alternativamente, podemos verificar qué centroides con buffer están completamente dentro de los polígonos originales de los distritos. En este caso, ambas GeoSeries están alineadas, y la verificación se realiza para cada fila.

```python
# Verificar qué centroides con buffer están completamente dentro de sus respectivos distritos
gdf["within"] = gdf["buffered_centroid"].within(gdf)
gdf["within"]

```

Podemos graficar los resultados en el mapa para confirmar el hallazgo.

```python
# Cambiar la geometría activa a los centroides con buffer
gdf = gdf.set_geometry("buffered_centroid")
# Usar un gráfico categórico y establecer la posición de la leyenda
ax = gdf.plot(
    "within", legend=True, categorical=True, legend_kwds={"loc": "upper left"}
)
# Pasar el primer gráfico y establecer el ancho de línea a 0.5
gdf["boundary"].plot(ax=ax, color="black", linewidth=0.5)

```

## Proyecciones

Cada GeoSeries tiene su Sistema de Referencia de Coordenadas (CRS) accesible en `GeoSeries.crs`. El CRS le dice a GeoPandas dónde están ubicadas las coordenadas de las geometrías en la superficie de la Tierra. En algunos casos, el CRS es geográfico, lo que significa que las coordenadas están en latitud y longitud. En esos casos, su CRS es WGS84, con el código de autoridad EPSG:4326. Veamos la proyección de nuestro GeoDataFrame de los distritos de NY.

```python
# Ver el sistema de referencia de coordenadas actual
gdf.crs

```

Las geometrías están en EPSG:2263 con coordenadas en pies. Podemos reproyectar fácilmente una GeoSeries a otro CRS, como EPSG:4326 usando `GeoSeries.to_crs()`.

```python
# Volver a la geometría original como activa
gdf = gdf.set_geometry("geometry")
# Reproyectar a WGS84 (EPSG:4326)
boroughs_4326 = gdf.to_crs("EPSG:4326")
# Graficar el resultado
boroughs_4326.plot()

```

```python
# Ver el nuevo sistema de referencia de coordenadas
boroughs_4326.crs

```

Nota la diferencia en las coordenadas a lo largo de los ejes del gráfico. Donde teníamos 120,000 - 280,000 (pies) antes, ahora tenemos 40.5 - 40.9 (grados). En este caso, `boroughs_4326` tiene una columna "geometry" en WGS84 pero todas las demás (con centroides, etc.) permanecen en el CRS original.

## Recursos adicionales

- Documentación oficial de GeoPandas: https://geopandas.org/
- Tutorial completo: https://geopandas.org/en/stable/getting_started/introduction.html
- Ejemplos prácticos: https://geopandas.org/en/stable/gallery/index.html
