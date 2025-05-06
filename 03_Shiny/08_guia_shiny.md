# **Guía Shiny**

## **1. Introducción y Configuración**

### **1.1. Introducción a Shiny para Python**

### **¿Qué es Shiny y para qué se utiliza?**

Shiny es un marco de desarrollo de aplicaciones web de código abierto que permite a los profesionales de datos y científicos construir aplicaciones web interactivas directamente desde Python (o R). Su propósito principal es transformar análisis de datos estáticos en herramientas dinámicas y explorables, como dashboards interactivos, visualizaciones de datos personalizadas y aplicaciones de soporte a la decisión, sin necesidad de tener conocimientos profundos en desarrollo web tradicional (HTML, CSS, JavaScript).

El núcleo de Shiny se basa en el paradigma de la **programación reactiva**. Esto significa que la aplicación "reacciona" automáticamente a los cambios en las entradas del usuario. Cuando un usuario interactúa con un control (por ejemplo, mueve un deslizador o selecciona una opción de un menú desplegable), Shiny recalcula automáticamente solo las partes de la aplicación que dependen de esa entrada y actualiza las salidas correspondientes (como gráficos o tablas). Este enfoque elimina la necesidad de gestionar manualmente el estado de la aplicación y escribir complejas funciones de devolución de llamada (callbacks), permitiendo a los desarrolladores centrarse en la lógica del análisis y la visualización.

### **Ventajas en el Análisis y Visualización de Datos**

En el contexto del análisis de datos con Python, Shiny ofrece varias ventajas significativas:

1. **Integración Nativa con Python:** Permite a los usuarios aprovechar todo el ecosistema de Python para ciencia de datos (pandas, Matplotlib, Seaborn, Plotly, scikit-learn, etc.) directamente en la construcción de la aplicación web.
2. **Interactividad sin Esfuerzo:** La programación reactiva simplifica enormemente la creación de interfaces interactivas. Los cambios en los controles de entrada (`inputs`) desencadenan automáticamente actualizaciones en los elementos de salida (`outputs`).
3. **Creación Rápida de Prototipos:** Facilita la conversión rápida de scripts de análisis en aplicaciones funcionales, ideal para compartir resultados preliminares o crear herramientas exploratorias.
4. **Comunicación Amplia:** Permite a los analistas y científicos de datos comunicar sus hallazgos a una audiencia más amplia (incluyendo aquellos sin conocimientos técnicos) a través de una interfaz web accesible.
5. **Eficiencia:** El motor reactivo de Shiny está diseñado para minimizar los recálculos innecesarios. Las salidas solo se renderizan bajo demanda y cuando sus dependencias cambian, lo que optimiza el rendimiento.
6. **Robustez y Escalabilidad:** Construido sobre tecnologías web modernas de Python como Starlette y asyncio, Shiny ofrece una base sólida para aplicaciones web robustas. Además, soporta personalización avanzada con CSS y JavaScript y cuenta con mecanismos como los Módulos Shiny para gestionar aplicaciones grandes y complejas.

Shiny se posiciona como una alternativa potente a otras herramientas de dashboarding en Python como Dash o Streamlit, ofreciendo una estructura clara (separación UI/Servidor en Core, o implícita en Express) y un modelo reactivo eficiente.

### **1.2. Preparación del Entorno**

### **Requisitos Previos**

Antes de comenzar a desarrollar con Shiny para Python, es necesario asegurarse de cumplir con ciertos requisitos:

- **Versión de Python:** Es recomendable utilizar una versión reciente y estable de Python 3 para asegurar la compatibilidad con las últimas características de Shiny y sus dependencias.
- **Conocimientos de Python y Pandas:** Esta guía asume que los estudiantes tienen un nivel intermedio de Python, incluyendo familiaridad con estructuras de datos, funciones y manejo básico de paquetes. Se espera también experiencia con la librería `pandas` para la manipulación y análisis de datos, ya que es fundamental en el flujo de trabajo de análisis de datos que Shiny busca potenciar.
- **Conocimientos de Visualización (Básico):** Se asume una comprensión básica de cómo crear gráficos utilizando librerías como Matplotlib o Seaborn, ya que serán las herramientas iniciales para generar visualizaciones dentro de las aplicaciones Shiny.

### **Configuración del Entorno de Desarrollo**

Un entorno de desarrollo bien configurado es esencial para trabajar eficientemente y evitar problemas comunes.

- **Entornos Virtuales (`venv`):** Es una práctica fundamental en el desarrollo con Python crear un entorno virtual aislado para cada proyecto. Esto evita conflictos entre las dependencias de diferentes proyectos y asegura que cada aplicación tenga su propio conjunto de paquetes con versiones específicas.
    - **Creación y Activación:** Se recomienda usar el módulo `venv` incorporado en Python 3. Los pasos típicos son :
        
        
    
    ```yaml
    # Navegar al directorio del proyecto
    cd mi_proyecto_shiny
    # Crear el entorno virtual (comúnmente llamado.venv)
    python3 -m venv.venv
    # Activar el entorno (Linux/macOS)
    source.venv/bin/activate
    # Activar el entorno (Windows - Command Prompt)
    .venv\Scripts\activate.bat
    # Activar el entorno (Windows - PowerShell)
    .venv\Scripts\Activate.ps1
    ```
    
    - La creación de entornos virtuales fomenta la reproducibilidad. Al aislar las dependencias, se asegura que la aplicación funcione de manera consistente en diferentes máquinas o al ser desplegada, ya que se utilizan exactamente las versiones de paquetes especificadas.
- **Instalación de Shiny y Dependencias:**
    - **Instalación Principal:** Una vez activado el entorno virtual, Shiny se instala fácilmente usando `pip`, el gestor de paquetes de Python.
        
        `pip install shiny`
        
    - **Librerías Esenciales de Ciencia de Datos:** Es necesario instalar las librerías fundamentales que se usarán junto con Shiny.`pandas` es omnipresente en el análisis de datos con Python , y `matplotlib`/`seaborn` son opciones comunes y robustas para la visualización inicial dentro de Shiny, compatibles con el decorador `@render.plot`.
        
        `pip install pandas matplotlib seaborn`
        
    - **Archivo `requirements.txt`:** Es una buena práctica registrar todas las dependencias del proyecto en un archivo llamado `requirements.txt`. Esto facilita la instalación de todas las dependencias necesarias de una sola vez y es crucial para la reproducibilidad y el despliegue de la aplicación.
        - Ejemplo de `requirements.txt`:
        
        ```yaml
        shiny
        pandas
        matplotlib
        seaborn
        ```
        
        - Para instalar desde este archivo :
            
            `pip install -r requirements.txt`
            
- **Entorno de Desarrollo Integrado (IDE) / Editor:**
    - Se recomienda **Visual Studio Code (VS Code)** junto con las extensiones oficiales de Python y Shiny. Estas extensiones proporcionan características útiles como resaltado de sintaxis específico para Shiny, autocompletado, y botones dedicados para ejecutar y depurar aplicaciones Shiny directamente desde el editor. VS Code también puede configurarse para realizar comprobaciones de tipos (`type checking`), lo que ayuda a detectar errores potenciales tempranamente.
    - Aunque otros editores de texto o IDEs de Python pueden usarse para escribir código Shiny , la integración específica en VS Code ofrece un flujo de trabajo más fluido, especialmente para principiantes.
    - **Alternativa Online:** Para pruebas rápidas o para empezar sin una instalación local, se puede mencionar **Shinylive**. Permite ejecutar y editar aplicaciones Shiny directamente en el navegador web utilizando WebAssembly. Sin embargo, para el desarrollo estructurado del curso, se recomienda el enfoque de desarrollo local.
- **Ejecutando una Aplicación Shiny:**
    - **Desde la Línea de Comandos:** El comando `shiny run` es la forma estándar de iniciar una aplicación.
    La opción `-reload` es particularmente útil durante el desarrollo, ya que reinicia automáticamente la aplicación cada vez que se guarda un cambio en el código fuente, acelerando el ciclo de iteración.
        
        
        ```yaml
        # Ejecución básica (asumiendo que el archivo principal es app.py)
        shiny run app.py
        # Ejecución con recarga automática y apertura del navegador
        shiny run --reload --launch-browser app.py
        ```
        
    
    - **Desde VS Code:** Con la extensión de Shiny instalada, aparece un botón "Run Shiny App" (o similar) en la interfaz del editor, que simplifica el proceso de ejecución.

La combinación de entornos virtuales (`venv`), un registro de dependencias (`requirements.txt`), un IDE potente como VS Code con extensiones específicas, y el comando `shiny run --reload` establece un flujo de trabajo de desarrollo eficiente, estándar y reproducible. Adoptar este flujo desde el principio es fundamental para los estudiantes que pasan de escribir scripts simples a construir aplicaciones más complejas, ya que previene problemas comunes como conflictos de dependencias y facilita la colaboración y el despliegue.

## **2. Fundamentos de Shiny**

### **2.1. Construyendo una Aplicación Shiny Básica**

### **Anatomía de una Aplicación Shiny**

Una aplicación Shiny para Python generalmente reside en un directorio que contiene un archivo principal, comúnmente llamado `app.py`, junto con cualquier otro archivo necesario (datos, scripts auxiliares, CSS, etc.).**7** Existen dos sintaxis principales para estructurar el código dentro de `app.py`:

1. **Shiny Core:** Define explícitamente `app_ui` (interfaz) y `server` (lógica), combinándolos con `App(app_ui, server)`. Esta separación clara es útil para entender la estructura fundamental.
2. **Shiny Express:** Una sintaxis más concisa donde los elementos de la UI se definen directamente en el nivel superior del script. La lógica del servidor se adjunta a funciones específicas mediante decoradores (`@render.*`) sin un bloque `def server(...)` explícito. Es a menudo preferida por su brevedad y se usará en los ejemplos de esta guía.

A continuación, se muestra la estructura mínima de una aplicación Shiny Express:

```python
# app.py
from shiny.express import ui

# La UI se define directamente en el nivel superior del script.
# ui.page_fluid crea una página básica que se ajusta al navegador.
ui.page_fluid(
    "¡Hola, Shiny!" # Contenido simple de la UI
)

# En Express, no hay una función 'server' explícita para este caso simple.
# La lógica reactiva se añadiría con funciones decoradas (@render.*).

```

### **Creando la Interfaz de Usuario (`ui`) con Express**

En Shiny Express, los elementos de la UI se declaran directamente en el script.

- **Contenedores de Página:** Se necesita una función de página principal. `ui.page_fluid` es un excelente punto de partida, creando una página cuyo ancho se adapta al navegador. Otros contenedores como `ui.page_fixed` (ancho fijo), `ui.page_sidebar` o `ui.page_navbar` también se pueden usar directamente.
- **Elementos HTML:** Se pueden añadir elementos HTML básicos como cadenas de texto o usando `ui.tags` (por ejemplo, `ui.tags.h1("Título Principal")`, `ui.tags.p("Un párrafo de texto.")`).

### **Definiendo la Lógica del Servidor con Express**

En Express, no hay una función `server` global. La lógica reactiva se define mediante funciones decoradas con `@render.*` o `@reactive.*`. Estas funciones acceden a los valores de entrada (`input`) y definen las salidas (`output`) implícitamente por su decorador y nombre (o explícitamente si es necesario).

### ***Ejercicio 1: ¡Hola, Shiny!***

- **Objetivo:** Crear y ejecutar la aplicación Shiny más básica posible usando Shiny Express.
- **Pasos:**
    1. Crear un nuevo directorio para el proyecto.
    2. Dentro del directorio, crear un archivo llamado `app.py`.
    3. Configurar un entorno virtual, activarlo e instalar `shiny` (`pip install shiny`).
    4. Copiar y pegar el código de la estructura mínima de Shiny Express en `app.py`.
    5. Abrir una terminal en el directorio del proyecto (con el entorno virtual activado).
    6. Ejecutar la aplicación usando `shiny run --reload app.py`.
- **Resultado Esperado:** Debería abrirse una ventana del navegador mostrando una página web simple con el texto "¡Hola, Shiny!".

### **2.2: Mostrando Visualizaciones de Datos Estáticas**

Una vez establecida la estructura básica, el siguiente paso es mostrar visualizaciones de datos. Nos centraremos en gráficos estáticos generados con Matplotlib o Seaborn usando Shiny Express.

### **Espacios Reservados para Salidas en la UI (`ui.output_*`)**

Para mostrar contenido generado dinámicamente (como un gráfico), se debe definir un espacio reservado en la interfaz de usuario.

- **`ui.output_plot("id_del_plot")`:** Reserva un espacio para mostrar un gráfico.
- **Importancia del `id`:** El argumento `id` (por ejemplo, `"id_del_plot"`) es crucial. Debe ser una cadena de texto única. En Express, este `id` debe coincidir con el nombre de la función `@render.plot` que genera el gráfico.

### **Funciones de Renderizado en el Servidor (`@render.*`)**

En Express, los decoradores `@render.*` se aplican a funciones definidas en el nivel superior del script para generar contenido dinámico.

- **`@render.plot`:** Se aplica a una función Python que genera un gráfico. La función debe devolver un objeto de gráfico compatible (figura/ejes de Matplotlib).
- **Vinculación UI-Servidor (Express):** Shiny Express conecta automáticamente la función decorada `mi_histograma()` con el elemento `ui.output_plot("mi_histograma")` porque tienen el mismo nombre/ID.

### **Integrando Matplotlib y Seaborn con Express**

El flujo de trabajo típico:

1. Definir un `ui.output_plot("mi_grafico")` en la sección de UI del script.
2. Definir una función llamada `mi_grafico` en el nivel superior del script, decorada con `@render.plot`.
3. Dentro de esa función, escribir el código Python para generar el gráfico (Matplotlib/Seaborn).
4. Asegurarse de que la función devuelva el objeto figura de Matplotlib (`fig`).

Ejemplo completo (usando Express y datos ficticios):

**Python**

```python
# app.py
from shiny.express import render, ui
import matplotlib.pyplot as plt
import numpy as np # Para generar datos de ejemplo

# UI: Define un título y un espacio para el gráfico
ui.tags.h2("Mi Gráfico Estático")

# Servidor (Express): Define la lógica para generar el gráfico
@render.plot # Decorador que indica que esta función genera un gráfico
def mi_histograma():
    # Código estándar de Matplotlib para crear un histograma
    datos = np.random.randn(200)
    fig, ax = plt.subplots()
    ax.hist(datos, bins=20)
    ax.set_title("Un Histograma Estático")
    ax.set_xlabel("Valor")
    ax.set_ylabel("Frecuencia")
    return fig # Devuelve el objeto figura de Matplotlib

```

- **Compatibilidad:** Librerías como Seaborn, plotnine (backend Matplotlib) y pandas `.plot()` funcionan bien con `@render.plot`.

### **Cargando y Usando Datos de Pandas**

Es común cargar datos desde archivos (CSV). Para datos estáticos, cárgalos una sola vez al inicio de la aplicación.

- **Ámbito de Carga:** Carga los datos en el nivel superior del script `app.py`, fuera de cualquier función reactiva. Este código se ejecuta solo una vez al iniciar la aplicación.
- **Eficiencia:** Evita recargar el archivo en cada interacción del usuario o ejecución de función reactiva, crucial para el rendimiento con datos grandes.
    
    

Ejemplo de carga de datos con Pandas en Express:

csv disponible en: 

```python
# app.py
from shiny.express import render, ui
import matplotlib.pyplot as plt
import pandas as pd
from pathlib import Path # Para manejo de rutas

# Carga de datos (nivel superior del script)
try:
    # Asume que penguins.csv está en el mismo directorio que app.py
    RUTA_DATOS = Path(__file__).parent / "penguins.csv"
    penguins_df = pd.read_csv(RUTA_DATOS)
    print("Datos de pingüinos cargados exitosamente.")
except FileNotFoundError:
    print("Error: penguins.csv no encontrado. Por favor, descárgalo.")
    penguins_df = pd.DataFrame() # DataFrame vacío para evitar errores

# --- Definición de UI ---
ui.tags.h2("Visualización de Datos de Pingüinos")

# --- Lógica del Servidor (Express) ---
@render.plot
def grafico_pinguinos():
    if not penguins_df.empty:
        # Crear gráfico usando el DataFrame cargado globalmente
        fig, ax = plt.subplots()
        penguins_df["bill_length_mm"].hist(ax=ax) # Ejemplo: Histograma
        ax.set_title("Distribución de Longitud del Pico (Pingüinos)")
        ax.set_xlabel("Longitud del Pico (mm)")
        ax.set_ylabel("Frecuencia")
        return fig
    else:
        # Mensaje si los datos no están disponibles
        fig, ax = plt.subplots()
        ax.text(0.5, 0.5, "Datos no disponibles", ha='center', va='center')
        return fig

```

### ***Ejercicio 2: Graficando Datos de Pingüinos***

- **Objetivo:** Cargar `penguins.csv` y mostrar un gráfico estático (histograma, dispersión) usando Shiny Express.
- **Pasos:**
    1. Obtener `penguins.csv` y guardarlo junto a `app.py`.
    2. Modificar `app.py` para cargar datos con `pandas` globalmente.
    3. Asegurar que la UI contenga `ui.output_plot("id_grafico")`.
    4. Implementar una función `id_grafico()` decorada con `@render.plot` que:
        - Acceda a `penguins_df`.
        - Genere un gráfico (ej. `penguins_df['flipper_length_mm'].hist()`).
        - Devuelva la figura Matplotlib.
    5. Ejecutar la aplicación.
- **Resultado Esperado:** Una página web mostrando un gráfico basado en los datos de pingüinos.

## **3. Interactividad y Estructura**

### **3.1. Haciendo tu Aplicación Reactiva**

La interactividad se logra con controles de entrada en la UI y vinculándolos a las salidas en el servidor mediante reactividad.

### **Widgets de Entrada: Recopilando Información del Usuario (`ui.input_*`)**

Son los componentes UI para que el usuario interactúe y proporcione valores.**6**

- **Sintaxis:** Funciones `ui.input_*()`.
- **Argumentos Clave:**
    - `id`: Cadena única que identifica el widget. Se usa para leer su valor.
    - `label`: Texto descriptivo mostrado al usuario.
    - Otros argumentos específicos (ej. `min`, `max`, `value` para slider; `choices`, `selected` para selector).

**Tabla 1: Widgets de Entrada Comunes en Shiny para Python**

| **Función (ui.input_*)** | **Propósito Principal** | **Argumentos Clave Adicionales** |
| --- | --- | --- |
| `ui.input_slider()` | Seleccionar un número o rango dentro de límites | `min`, `max`, `value` (valor inicial), `step` |
| `ui.input_select()` | Seleccionar uno o más ítems de una lista desplegable | `choices` (lista de opciones), `selected`, `multiple` |
| `ui.input_selectize()` | Similar a `select`, con búsqueda y mejor interfaz | `choices`, `selected`, `multiple` |
| `ui.input_text()` | Ingresar una cadena de texto corta | `placeholder`, `value` (texto inicial) |
| `ui.input_numeric()` | Ingresar un valor numérico | `value`, `min`, `max`, `step` |
| `ui.input_checkbox()` | Marcar/desmarcar una opción (Booleano True/False) | `value` (estado inicial True/False) |
| `ui.input_radio_buttons()` | Seleccionar una opción de un conjunto mutuamente excluyente | `choices`, `selected`, `inline` |
| `ui.input_date()` | Seleccionar una fecha | `value`, `min`, `max`, `format` |
| `ui.input_action_button()` | Botón para disparar una acción específica | `icon` |
| `ui.input_file()` | Permitir al usuario cargar un archivo | `multiple`, `accept` (tipos de archivo) |

Esta tabla sirve como referencia rápida para identificar y usar controles de entrada adecuados.

### **Comprendiendo la Reactividad: Vinculando Entradas y Salidas**

La reactividad conecta los valores de entrada con las funciones de renderizado.

- **Acceso a Valores de Entrada:** Se accede al valor actual de un widget usando `input.id_del_widget()`. Para `ui.input_slider("bins",...)`, se lee con `input.bins()`.
- **Dependencia Automática:** Cualquier función decorada con `@render.*` (o `@reactive.calc`) que llame a `input.algun_id()` establece una **dependencia reactiva**. Shiny sabe que esta función debe re-ejecutarse cuando `input.algun_id()` cambie.

Ejemplo simple de reactividad con Express (Texto que actualiza con Slider):

**Python**

```python
# app.py
from shiny.express import input, render, ui

# Widget de entrada: Slider
ui.input_slider("n", "Selecciona un número:", min=0, max=100, value=50)

# Función de renderizado para la salida de texto
@render.text
def valor_slider_display():
    # Lee el valor actual del slider 'n' de forma reactiva
    valor_actual = input.n()
    # Devuelve una cadena formateada con el valor
    return f"El valor seleccionado en el slider es: {valor_actual}"

```

Cuando el usuario mueve el slider `n`, `input.n()` cambia. Shiny detecta que `valor_slider_display` depende de `input.n()` y la re-ejecuta.

### **Cálculos Reactivos (`@reactive.calc`)**

Para evitar cálculos repetitivos, se usa `@reactive.calc` para cálculos intermedios.

- **Funcionamiento:** Una función con `@reactive.calc`:
    - Se ejecuta solo si sus dependencias cambian.
    - **Almacena en caché su resultado.** Devuelve el caché si las dependencias no han cambiado.
    - Actúa como fuente reactiva: otras funciones `@render.*` o `@reactive.calc` pueden llamarla y depender de ella.

Ejemplo de uso para filtrar datos con Express:

```python
from shiny import reactive
from shiny.express import input, render, ui
import pandas as pd
from pathlib import Path
import matplotlib.pyplot as plt

# --- Cargar datos ---
try:
    RUTA_DATOS = Path(__file__).parent / "penguins.csv"
    penguins_df = pd.read_csv(RUTA_DATOS)
except FileNotFoundError:
    penguins_df = pd.DataFrame()

# --- UI ---
ui.page_opts(title="Explorador de Pingüinos", fillable=True)

ui.input_selectize(
    "species",
    "Selecciona una especie:",
    choices=sorted(penguins_df["species"].dropna().unique()) if not penguins_df.empty else [],
)

ui.input_selectize(
    "island",
    "Selecciona una isla:",
    choices=sorted(penguins_df["island"].dropna().unique()) if not penguins_df.empty else [],
)

# --- Reactividad ---
@reactive.calc
def datos_filtrados():
    if penguins_df.empty:
        return pd.DataFrame()
    especie = input.species()
    isla = input.island()
    if especie and isla:
        return penguins_df[
            (penguins_df["species"] == especie) & (penguins_df["island"] == isla)
        ]
    return pd.DataFrame()

@render.plot
def grafico_filtrado():
    df = datos_filtrados()
    fig, ax = plt.subplots()
    if not df.empty:
        df.plot(kind='scatter', x='bill_length_mm', y='bill_depth_mm', ax=ax)
        ax.set_title(f"{input.species()} en {input.island()}")
    else:
        ax.text(0.5, 0.5, "Seleccione especie e isla", ha='center', va='center')
    return fig

@render.data_frame
def tabla_filtrada():
    df = datos_filtrados()
    return df if not df.empty else pd.DataFrame()

```

Usar `@reactive.calc` aquí hace el código más eficiente (el filtrado se hace una vez) y modular (separa la lógica de datos de la de presentación).

### **3.2. Diseñando Interfaces de Usuario con Layouts**

Los layouts organizan la UI de forma estructurada y estética.

### **Motivación para Usar Layouts**

- Organización, claridad visual, uso eficiente del espacio, mejor experiencia de usuario.

### **Layouts con Barra Lateral (Sidebar)**

Común en dashboards: barra lateral para controles, área principal para resultados.

- **`ui.page_sidebar(...)`:** Función de página de alto nivel que crea esta estructura.
- **`ui.layout_sidebar(...)`:** Permite crear estructura de barra lateral *dentro* de otros contenedores (tarjetas, pestañas).
- **`ui.sidebar(...)`:** Define la barra lateral. Acepta contenido y parámetros como `position`, `open`, `width`, `title`, `bg`.
- **Uso con Express:** Estas funciones se usan a menudo como **context managers** (`with...:`).
    
    
    ```python
    from shiny.express import input, render, ui
    import matplotlib.pyplot as plt
    
    # --- UI ---
    
    with ui.sidebar(title="Panel de Control", bg="#f8f8f8"):
        ui.tags.h4("Controles")
        ui.input_slider("alpha", "Transparencia", 0, 1, 0.5)
    
    # Área principal
    ui.tags.h3("Resultados Principales")
    
    # --- Lógica del servidor ---
    @render.plot
    def grafico_principal():
        fig, ax = plt.subplots()
        ax.text(0.5, 0.5, f"Gráfico (Alpha={input.alpha():.2f})", ha='center', va='center')
        return fig
    
    ```
    

### **Navegación por Pestañas (Tabs)**

Organiza secciones distintas en pestañas.

- **`ui.navset_tab(...)`:** Crea un conjunto de pestañas. Variantes: `ui.navset_pill`, `ui.navset_card_tab`, etc..
- **`ui.nav_panel("Título",...)`:** Define cada pestaña individual.
- **Uso con Express:** Se usan como context managers.
    
    ```python
    import plotly.express as px
    from shiny.express import input, render, ui
    from shinywidgets import render_widget
    
    ui.page_opts(title="Multi-page example", fillable=True)
    
    with ui.sidebar():
        ui.input_select("var", "Select variable", choices=["total_bill", "tip"])
    
    with ui.nav_panel("Plot"):
        @render_widget
        def hist():
            return px.histogram(px.data.tips(), input.var())
    
    with ui.nav_panel("Table"):
        @render.data_frame
        def table():
            return px.data.tips()
    ```
    

### **Organización del Contenido con Tarjetas (`ui.card`)**

Contenedores versátiles para agrupar elementos visualmente.**1**

- **Uso:** Se pueden colocar dentro de layouts y contener otros elementos UI.
- **Componentes:** `ui.card_header()`, `ui.card_footer()`.
- **Uso con Express:** Como context manager
    
    ```python
    from shiny.express import ui
    
    ui.page_opts(fillable=True)
    
    with ui.layout_columns():  
        with ui.card():  
            ui.card_header("Card 1 header")
            ui.p("Card 1 body")
            ui.input_slider("slider", "Slider", 0, 10, 5)
    
        with ui.card():  
            ui.card_header("Card 2 header")
            ui.p("Card 2 body")
            ui.input_text("text", "Add text", "")
    
    ```
    

### **Otras Opciones de Layout**

- **`ui.layout_columns(...)`:** Columnas responsivas (sistema de 12 unidades).
- **`ui.row(...)` / `ui.column(...)`:** Sistema más antiguo para filas/columnas.

## **Parte 4. Buenas Prácticas y Próximos Pasos**

### **4.1. Estructurando Aplicaciones Shiny Efectivamente**

- **Separación UI/Lógica (Implícita en Express):** Aunque no hay bloques `app_ui`/`server` separados, la estructura de Express (UI en nivel superior, lógica en funciones decoradas) mantiene una separación conceptual.
- **Uso de Funciones Auxiliares:** Descomponer secciones complejas de UI o cálculos en funciones Python estándar dentro de `app.py` sigue siendo una buena práctica.
- **Comentarios en el Código:** Fomentar comentarios claros para explicar UI, lógica y reactividad.

### **4.2. Introducción a los Módulos Shiny**

Para aplicaciones grandes, los Módulos Shiny son la solución para la escalabilidad.

- **El Problema de Escalar:** Reutilizar componentes UI/servidor directamente causa conflictos de `id`.
- **¿Qué son los Módulos?:** Encapsulan UI y lógica de servidor en unidades reutilizables con **espacio de nombres (namespace)** propio, evitando colisiones de `id`. Son específicos de Shiny, no módulos Python estándar.
- **Beneficios:** Reutilización, organización, testing, colaboración.
- **Concepto Básico (Express):** Se crea un módulo usando el decorador `@module` en una función que define la UI y la lógica del servidor. Al usar el módulo, se le da un `id` único.
- **Cuándo Usarlos:** Componentes reutilizables, `app.py` muy largo, facilitar pruebas/colaboración.

### **4.3. Consejos para Código Legible y Mantenible**

- Nombres descriptivos para variables, funciones, IDs.
- Estilo consistente (PEP 8).
- Funciones reactivas enfocadas; dividir si son complejas.
- Comentarios claros.

### **4.4. Recursos Adicionales para el Aprendizaje**

- **Documentación Oficial Shiny para Python:** Fuente principal.
- **Galería de Ejemplos:** Inspiración y código funcional.
- **Tutoriales Externos:** Guía Appsilon , DataCamp , blogs.
- **Shinylive:** Explorar ejemplos, compartir prototipos.
- **Comunidad:** Shiny 4 All.

### **4.5. Panorama General del Despliegue**

Alojar la aplicación para hacerla accesible.

- **Opciones Comunes:**
    - **Shinylive:** Para apps simples sin servidor Python backend, usando WebAssembly. Despliegue en hosts estáticos (GitHub Pages, Netlify). Limitaciones: paquetes, tamaño descarga, código visible. Comando: `shinylive export mi_app dir_salida`.
    - **shinyapps.io:** Servicio gestionado por Posit (planes gratuitos/pago). Sencillo para R y Python. Requiere `rsconnect-python`.
    - **Posit Connect:** Plataforma empresarial (on-premise/cloud). Soporta Shiny, Quarto, Dash, etc..
    - **Shiny Server Open Source:** Servidor gratuito (Linux). Requiere config manual. Soporta Python desde v1.5.22.
    - **Otras Plataformas (ASGI):** Cualquier host ASGI (Uvicorn, Gunicorn), servicios cloud (Heroku, AWS, GCP), Hugging Face Spaces.

### **4.6. Ideas para Proyectos de Práctica**

- Convertir análisis estáticos existentes a Shiny interactivo.
- Explorar nuevos datos con una app Shiny interactiva.
- Integrar otras librerías de visualización (Plotly, Bokeh).
- Construir un mini-dashboard con layout (sidebar/tabs), `ui.card`, `ui.value_box`.
- Permitir carga de datos con `ui.input_file`.