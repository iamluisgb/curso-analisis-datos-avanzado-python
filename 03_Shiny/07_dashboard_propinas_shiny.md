# Dashboard de Propinas con Shiny

En esta guía, aprenderás a construir paso a paso un dashboard interactivo que analiza datos de propinas en restaurantes utilizando Shiny para Python.

## Objetivos de Aprendizaje

Al finalizar esta guía, serás capaz de:

- Estructurar una aplicación Shiny para Python
- Implementar elementos de interfaz de usuario reactiva
- Crear visualizaciones interactivas con Plotly
- Filtrar datos dinámicamente en respuesta a entradas del usuario
- Personalizar la apariencia de tu aplicación con CSS

## Requisitos Previos

- Conocimientos básicos de Python
- Familiaridad con pandas para manipulación de datos
- Nociones básicas de visualización de datos (preferiblemente con plotly)

## Estructura del Proyecto

Crearemos los siguientes archivos:

```
dashboard-tips/
├── app.py            # Archivo principal de la aplicación
├── shared.py         # Módulo para cargar datos
├── styles.css        # Estilos personalizados
├── _template.json    # Metadatos de la aplicación
├── requirements.txt  # Dependencias del proyecto
└── tips.csv          # Conjunto de datos

```

## Paso 1: Preparación del Entorno

### Creando el archivo requirements.txt

Primero, vamos a crear un archivo `requirements.txt` para gestionar nuestras dependencias. Este archivo ayuda a asegurar que todos los estudiantes tengan exactamente las mismas versiones de las bibliotecas necesarias.

Crea un archivo llamado `requirements.txt` con el siguiente contenido:

```
faicons
shiny
shinywidgets
plotly
pandas
ridgeplot
seaborn

```

### Instalación de Dependencias

Ahora, instala las dependencias utilizando pip:

```bash
pip install -r requirements.txt

```

Este comando instalará todas las bibliotecas listadas en el archivo requirements.txt.

### Obtención de los Datos

Para este proyecto, utilizaremos el conjunto de datos de propinas que es comúnmente utilizado en ejemplos de visualización. Si no lo tienes, puedes obtenerlo desde seaborn:

```python
# Ejecuta este código una vez para generar el archivo de datos
import seaborn as sns
tips = sns.load_dataset("tips")
tips.to_csv("tips.csv", index=False)

```

Este dataset contiene información sobre propinas en un restaurante, incluyendo:

- `total_bill`: Importe total de la factura
- `tip`: Cantidad de propina
- `sex`: Género del cliente
- `smoker`: Si el cliente es fumador o no
- `day`: Día de la semana
- `time`: Momento del día (almuerzo o cena)
- `size`: Tamaño del grupo

## Paso 2: Configurando el Módulo de Datos Compartidos

Crea un archivo llamado `shared.py` con el siguiente contenido:

```python
from pathlib import Path
import pandas as pd

# Define la ruta al directorio de la aplicación
app_dir = Path(__file__).parent

# Carga el conjunto de datos de propinas
tips = pd.read_csv(app_dir / "tips.csv")

```

Este módulo hace dos cosas importantes:

1. Define `app_dir` que apunta al directorio actual de la aplicación
2. Carga los datos de propinas desde el archivo CSV

## Paso 3: Creando la Aplicación Principal

Ahora, vamos a crear el archivo principal de la aplicación, `app.py`. Lo crearemos paso a paso, explicando cada sección:

### Parte 1: Importaciones y Configuración Inicial

```python
import faicons as fa
import plotly.express as px

# Cargar datos y computar valores estáticos
from shared import app_dir, tips
from shiny import reactive, render
from shiny.express import input, ui
from shinywidgets import render_plotly

# Calcular el rango de valores de facturas para el control deslizante
bill_rng = (min(tips.total_bill), max(tips.total_bill))

```

En esta sección:

- Importamos las bibliotecas necesarias
- Cargamos los datos de propinas desde nuestro módulo compartido
- Calculamos el rango de valores de las facturas para usarlo en el control deslizante

### Parte 2: Configuración de la Página y Barra Lateral

```python
# Añadir título de página y opciones
ui.page_opts(title="Restaurant tipping", fillable=True)

# Crear la barra lateral con controles de filtrado
with ui.sidebar(open="desktop"):
    ui.input_slider(
        "total_bill",                # ID del input
        "Bill amount",               # Etiqueta para el usuario
        min=bill_rng[0],             # Valor mínimo
        max=bill_rng[1],             # Valor máximo
        value=bill_rng,              # Valor inicial (rango completo)
        pre="$",                     # Prefijo para los valores
    )
    ui.input_checkbox_group(
        "time",                      # ID del input
        "Food service",              # Etiqueta para el usuario
        ["Lunch", "Dinner"],         # Opciones disponibles
        selected=["Lunch", "Dinner"], # Opciones seleccionadas inicialmente
        inline=True,                 # Mostrar horizontalmente
    )
    ui.input_action_button("reset", "Reset filter") # Botón para reiniciar filtros

```

En esta sección:

- Configuramos el título de la página
- Creamos una barra lateral con controles interactivos:
    - Un control deslizante para filtrar por importe de factura
    - Un grupo de casillas de verificación para filtrar por momento del servicio
    - Un botón para restablecer los filtros

### Parte 3: Definición de Iconos

```python
# Definir iconos para la interfaz
ICONS = {
    "user": fa.icon_svg("user", "regular"),
    "wallet": fa.icon_svg("wallet"),
    "currency-dollar": fa.icon_svg("dollar-sign"),
    "ellipsis": fa.icon_svg("ellipsis"),
}

```

Aquí definimos iconos SVG que utilizaremos en los elementos visuales de la aplicación.

### Parte 4: Cajas de Valores

```python
# Crear fila de cajas de valores
with ui.layout_columns(fill=False):
    # Primera caja de valor: Total de propinas
    with ui.value_box(showcase=ICONS["user"]):
        "Total tippers"

        @render.express
        def total_tippers():
            tips_data().shape[0]  # Contar filas en los datos filtrados

    # Segunda caja de valor: Propina promedio
    with ui.value_box(showcase=ICONS["wallet"]):
        "Average tip"

        @render.express
        def average_tip():
            d = tips_data()
            if d.shape[0] > 0:
                perc = d.tip / d.total_bill  # Calcular porcentaje de propina
                f"{perc.mean():.1%}"         # Formatear como porcentaje

    # Tercera caja de valor: Factura promedio
    with ui.value_box(showcase=ICONS["currency-dollar"]):
        "Average bill"

        @render.express
        def average_bill():
            d = tips_data()
            if d.shape[0] > 0:
                bill = d.total_bill.mean()  # Calcular factura promedio
                f"${bill:.2f}"              # Formatear como moneda

```

En esta sección, creamos tres "cajas de valores" que muestran estadísticas clave:

- Número total de propinas (clientes)
- Porcentaje promedio de propina
- Importe promedio de la factura

Cada caja utiliza la función `@render.express` para renderizar dinámicamente el valor en función de los datos filtrados.

### Parte 5: Diseño Principal con Tarjetas

```python
# Crear diseño principal con tres tarjetas
with ui.layout_columns(col_widths=[6, 6, 12]):
    # Primera tarjeta: Tabla de datos
    with ui.card(full_screen=True):
        ui.card_header("Tips data")

        @render.data_frame
        def table():
            return render.DataGrid(tips_data())

```

Aquí comenzamos el diseño principal y creamos la primera tarjeta que contiene una tabla con los datos de propinas filtrados.

### Parte 6: Gráfico de Dispersión

```python
    # Segunda tarjeta: Gráfico de dispersión
    with ui.card(full_screen=True):
        with ui.card_header(class_="d-flex justify-content-between align-items-center"):
            "Total bill vs tip"
            # Menú emergente para opciones de color
            with ui.popover(title="Add a color variable", placement="top"):
                ICONS["ellipsis"]
                ui.input_radio_buttons(
                    "scatter_color",
                    None,
                    ["none", "sex", "smoker", "day", "time"],
                    inline=True,
                )

        # Renderizar el gráfico de dispersión
        @render_plotly
        def scatterplot():
            color = input.scatter_color()
            return px.scatter(
                tips_data(),
                x="total_bill",
                y="tip",
                color=None if color == "none" else color,
                trendline="lowess",  # Añadir línea de tendencia
            )

```

Esta segunda tarjeta contiene:

- Un gráfico de dispersión que muestra la relación entre el importe de la factura y la propina
- Un menú emergente para colorear los puntos según diferentes variables (género, fumador, día, momento)
- Una línea de tendencia LOWESS para mostrar la relación general

### Parte 7: Gráfico de Densidad (Ridgeplot)

```python
    # Tercera tarjeta: Gráfico de densidad (ridgeplot)
    with ui.card(full_screen=True):
        with ui.card_header(class_="d-flex justify-content-between align-items-center"):
            "Tip percentages"
            # Menú emergente para opciones de división
            with ui.popover(title="Add a color variable"):
                ICONS["ellipsis"]
                ui.input_radio_buttons(
                    "tip_perc_y",
                    "Split by:",
                    ["sex", "smoker", "day", "time"],
                    selected="day",  # Valor predeterminado
                    inline=True,
                )

        # Renderizar el gráfico de densidad
        @render_plotly
        def tip_perc():
            from ridgeplot import ridgeplot  # Importamos la función ridgeplot

            # Preparar datos
            dat = tips_data()
            dat["percent"] = dat.tip / dat.total_bill  # Calcular porcentaje de propina
            yvar = input.tip_perc_y()  # Variable para dividir
            uvals = dat[yvar].unique()  # Valores únicos de esa variable

            # Crear muestras para cada valor único
            samples = [[dat.percent[dat[yvar] == val]] for val in uvals]

            # Crear el gráfico ridgeplot
            plt = ridgeplot(
                samples=samples,
                labels=uvals,
                bandwidth=0.01,
                colorscale="viridis",
                colormode="row-index",
            )

            # Ajustar la leyenda
            plt.update_layout(
                legend=dict(
                    orientation="h", yanchor="bottom", y=1.02, xanchor="center", x=0.5
                )
            )

            return plt

```

Esta tercera tarjeta contiene:

- Un gráfico de tipo "ridgeplot" que muestra la distribución de los porcentajes de propina
- Opciones para dividir los datos por diferentes variables (género, fumador, día, momento)
- Personalización del diseño de la leyenda

### Parte 8: Inclusión de Estilos CSS

```python
# Incluir estilos CSS personalizados
ui.include_css(app_dir / "styles.css")

```

Esta línea incluye los estilos CSS personalizados desde el archivo `styles.css`.

### Parte 9: Reactividad y Cálculos

```python
# --------------------------------------------------------
# Cálculos reactivos y efectos
# --------------------------------------------------------

# Función reactiva para filtrar datos según entradas del usuario
@reactive.calc
def tips_data():
    bill = input.total_bill()  # Obtener rango de facturas seleccionado
    idx1 = tips.total_bill.between(bill[0], bill[1])  # Filtrar por factura
    idx2 = tips.time.isin(input.time())  # Filtrar por momento
    return tips[idx1 & idx2]  # Devolver datos filtrados

# Efecto reactivo para restablecer filtros cuando se hace clic en el botón
@reactive.effect
@reactive.event(input.reset)  # Activar cuando se haga clic en "reset"
def _():
    ui.update_slider("total_bill", value=bill_rng)  # Restablecer control deslizante
    ui.update_checkbox_group("time", selected=["Lunch", "Dinner"])  # Restablecer casillas

```

En esta sección, definimos:

- Un cálculo reactivo `tips_data()` que filtra los datos según los valores de entrada del usuario
- Un efecto reactivo que restablece los filtros cuando se hace clic en el botón de reinicio

## Paso 4: Creando los Estilos CSS

Crea un archivo llamado `styles.css` con el siguiente contenido:

```css
:root {
  --bslib-sidebar-main-bg: #f8f8f8;  /* Color de fondo para la barra lateral */
}

.popover {
  --bs-popover-header-bg: #222;      /* Color de fondo para cabeceras de popovers */
  --bs-popover-header-color: #fff;   /* Color de texto para cabeceras de popovers */
}

.popover .btn-close {
  filter: var(--bs-btn-close-white-filter);  /* Filtro para botón de cierre */
}

```

Estos estilos:

- Personalizan el fondo de la barra lateral (color gris claro)
- Dan formato a los elementos emergentes (fondo oscuro con texto blanco)
- Ajustan el botón de cierre para que sea visible sobre el fondo oscuro

## Paso 5: Creando el Archivo de Metadatos

Crea un archivo llamado `_template.json` con el siguiente contenido:

```json
{
  "id": "dashboard-tips",
  "title": "Restaurant tips dashboard",
  "description": "An intermediate dashboard with input filters, value boxes, a plot, and table."
}

```

Este archivo proporciona metadatos para la aplicación, que pueden ser útiles si la despliegas en un servicio de Shiny o la compartes con otros.

## Paso 6: Ejecutando la Aplicación

Para ejecutar la aplicación, guarda todos los archivos en el mismo directorio y ejecuta el siguiente comando en la terminal:

```bash
shiny run app.py

```

La aplicación debería estar disponible en tu navegador en `http://localhost:8000` o en la dirección que Shiny indique en la terminal.

## Conceptos Clave de Shiny

Ahora que has construido la aplicación, veamos algunos conceptos clave que has aprendido:

### 1. Modelo de Programación Reactiva

Shiny utiliza un paradigma de programación reactiva donde:

- **Entradas**: Son elementos de UI que capturan interacciones del usuario (sliders, checkboxes, etc.)
- **Salidas**: Son elementos que muestran resultados (gráficos, tablas, texto)
- **Reactividad**: Es el mecanismo que conecta entradas y salidas, actualizando automáticamente las salidas cuando cambian las entradas

### 2. Elementos de UI Importantes

- **Layout**: `ui.layout_columns()` para organizar elementos en columnas
- **Contenedores**: `ui.sidebar()`, `ui.card()` para agrupar elementos relacionados
- **Entradas**: `ui.input_slider()`, `ui.input_checkbox_group()`, `ui.input_radio_buttons()`
- **Salidas**: `ui.value_box()`, `render.data_frame()`, `render_plotly()`

### 3. Funciones Reactivas

- **@reactive.calc**: Para cálculos que dependen de entradas y se actualizan automáticamente
- **@reactive.effect**: Para efectos secundarios (como actualizar controles)
- **@reactive.event**: Para especificar cuándo debe ejecutarse un efecto

### 4. Renderers

- **@render.express**: Para renderizar valores simples
- **@render.data_frame**: Para renderizar tablas de datos
- **@render_plotly**: Para renderizar gráficos interactivos de Plotly

## Ejercicios para Practicar

Para consolidar tu aprendizaje, intenta estos ejercicios:

1. **Añadir otro filtro**: Agrega un filtro para el día de la semana (`day`) en la barra lateral
2. **Añadir otra caja de valor**: Crea una nueva caja de valor que muestre el tamaño promedio del grupo (`size`)
3. **Modificar el gráfico de dispersión**: Mejora el gráfico de dispersión entre factura total y propina añadiendo una opción en el menú emergente que permita al usuario visualizar el tamaño del grupo (`size`) mediante el tamaño de los puntos. Esto debe ser configurable mediante un checkbox que el usuario pueda activar o desactivar.
4. **Personalizar estilos**: Modifica el archivo CSS para cambiar los colores de la interfaz
5. **Añadir un gráfico de barras**: Crea una nueva tarjeta con un gráfico de barras que muestre el total de propinas por día de la semana