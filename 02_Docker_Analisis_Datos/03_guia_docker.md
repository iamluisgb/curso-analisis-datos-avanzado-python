# Guía Completa de Docker: De Principiante a Profesional

## 1. Introducción a Docker

Docker es la tecnología líder en el mundo de los contenedores que ha revolucionado el desarrollo y despliegue de aplicaciones. Esta guía te proporcionará todo lo necesario para comprender y utilizar Docker de manera efectiva en tus proyectos.

### 1.1 ¿Por qué Docker?

El desarrollo profesional de software enfrenta tres grandes desafíos:

- **Construir**: Escribir código en la máquina del desarrollador.
- **Distribuir**: Llevar la aplicación donde se va a desplegar.
- **Ejecutar**: Implementar la solución en el ambiente de producción.

Docker es una herramienta que nos permite abordar estos tres desafíos de manera unificada, permitiéndonos construir, distribuir y ejecutar cualquier aplicación en cualquier entorno.

### 1.2 Virtualización vs Contenedores

### Virtualización tradicional

La virtualización es la creación, a través de software, de una versión virtual de algún recurso tecnológico, como puede ser una plataforma de hardware, un sistema operativo, un dispositivo de almacenamiento o cualquier otro recurso de red.

**Limitaciones de las máquinas virtuales:**

- **Peso**: Duplican archivos comunes como sistemas operativos.
- **Costes de administración**: Requieren mantenimiento como cualquier otra computadora.
- **Múltiples formatos**: VDI, VMDK, VHD, etc.
- **Arranque lento**: Tiempo de inicio considerable.
- **Consumo de recursos**: Alto uso de CPU, memoria y almacenamiento.

### Contenedores

Los contenedores también resuelven los tres desafíos del desarrollo de software, pero en lugar de albergar un sistema operativo completo, comparten los recursos del sistema operativo del host sobre el que se ejecutan.

**Ventajas de los contenedores:**

- **Creación y despliegue ágil**: Mayor facilidad y eficiencia al crear imágenes de contenedor.
- **CI/CD**: Facilitan los procesos de integración y despliegue continuos gracias a imágenes inmutables.
- **Separación Dev/Ops**: Desacopla la aplicación de la infraestructura.
- **Observabilidad**: Proporciona métricas del sistema y de la aplicación.
- **Consistencia entre entornos**: Garantiza que la aplicación funcione igual en desarrollo y producción.
- **Portabilidad multiplataforma**: Funciona en diferentes sistemas operativos y nubes.
- **Enfoque en aplicaciones**: Eleva el nivel de abstracción del sistema operativo.
- **Microservicios**: Facilita arquitecturas distribuidas y escalables.
- **Aislamiento de recursos**: Hace el rendimiento más predecible.
- **Eficiencia**: Permite mayor densidad y mejor utilización de recursos.

## 2. Arquitectura y componentes de Docker

Docker está compuesto por varios elementos fundamentales:

### 2.1 Componentes principales

- **Docker Engine**: El núcleo de Docker que incluye:
    - **Docker daemon (dockerd)**: El servicio principal que gestiona los objetos Docker.
    - **API REST**: Permite la comunicación con el daemon mediante HTTP.
    - **Cliente de Docker (CLI)**: Interfaz de línea de comandos para interactuar con Docker.

### 2.2 Objetos Docker

- **Contenedores**: Instancias ejecutables de imágenes que encapsulan aplicaciones y sus dependencias.
- **Imágenes**: Plantillas de solo lectura que capturan el estado de un contenedor y sirven para crearlos.
- **Volúmenes**: Mecanismos para la persistencia de datos fuera de los contenedores.
- **Redes**: Permiten la comunicación entre contenedores y con el mundo exterior.

## 3. Instalación de Docker

Para instalar Docker, es recomendable seguir la [documentación oficial](https://docs.docker.com/get-docker/) según tu sistema operativo.

### 3.1 Verificación de la instalación

Una vez instalado, verifica la versión y la información general:

```bash
docker --version
docker info

```

## 4. Primeros pasos con Docker

### 4.1 Tu primer contenedor

Ejecuta el siguiente comando para crear tu primer contenedor:

```bash
docker run hello-world

```

Este comando descarga la imagen `hello-world` si no está disponible localmente, crea un contenedor a partir de ella y lo ejecuta. Verás un mensaje confirmando que Docker está funcionando correctamente.

### 4.2 Conceptos clave sobre contenedores

- Un contenedor es una agrupación de procesos que corren de forma aislada.
- Los procesos dentro de un contenedor están limitados a los recursos que se les asignan.
- Corren de forma nativa en Linux (en Windows y macOS utilizan una máquina virtual ligera).
- Cada contenedor tiene un identificador único.

## 5. Comandos básicos de Docker

### 5.1 Gestión de contenedores

```bash
# Listar contenedores en ejecución
docker ps

# Listar todos los contenedores (incluyendo los detenidos)
docker ps -a

# Ver información detallada de un contenedor
docker inspect <ID o nombre>

# Crear un contenedor con nombre personalizado
docker run --name mi-contenedor nginx

# Renombrar un contenedor
docker rename mi-contenedor nuevo-nombre

# Eliminar un contenedor
docker rm <ID o nombre>

# Eliminar todos los contenedores detenidos
docker container prune

# Detener un contenedor
docker stop <ID o nombre>

# Iniciar un contenedor detenido
docker start <ID o nombre>

# Reiniciar un contenedor
docker restart <ID o nombre>

```

### 5.2 Contenedores interactivos

```bash
# Crear y acceder a un contenedor de Ubuntu
docker run -it ubuntu

# Ejecutar un comando en un contenedor en ejecución
docker exec -it <ID o nombre> <comando>

# Ejemplo: acceder a la shell de un contenedor
docker exec -it mi-contenedor bash

```

## 6. Ciclo de vida de un contenedor

El ciclo de vida de un contenedor está determinado por el proceso principal (Main process).

### 6.1 Procesos en contenedores

- **Main process**: Proceso principal del contenedor. Si termina, el contenedor se detiene.
- **Sub processes**: Procesos secundarios. Si fallan, el contenedor sigue ejecutándose mientras el Main process esté activo.

### 6.2 Ejemplos de ciclo de vida

```bash
# Crear un contenedor que se mantenga en ejecución
docker run --name siempre-activo -d ubuntu tail -f /dev/null

# Verificar el ID del proceso principal
docker inspect --format '{{.State.Pid}}' siempre-activo

# Entrar al contenedor
docker exec -it siempre-activo bash

```

El contenedor permanece siempre activo porque `tail -f /dev/null` crea un proceso que nunca termina. Este comando intenta continuamente leer nuevos datos de `/dev/null` (un archivo especial siempre vacío) y, como el proceso principal del contenedor no finaliza, Docker mantiene el contenedor en ejecución indefinidamente.

## 7. Exposición de puertos

Los contenedores tienen su propia interfaz de red virtual. Para acceder a servicios dentro de un contenedor:

```bash
# Exponer el puerto 80 del contenedor en el puerto 8080 del host
docker run -d --name servidor-web -p 8080:80 nginx

```

Ahora puedes acceder al servidor web Nginx desde tu navegador usando:

- `http://localhost:8080`
- `http://0.0.0.0:8080`
- `http://127.0.0.1:8080`

## 8. Persistencia de datos en Docker

Docker ofrece principalmente dos formas de persistir datos:

### 8.1 Bind Mounts

Conectan directorios o archivos del host con el contenedor:

```bash
# Montar un directorio local en un contenedor de MongoDB
docker run -d --name mongodb \
  -v /ruta/local/datos:/data/db \
  mongo

```

### 8.2 Volumes

Gestionados completamente por Docker, son la opción recomendada:

```bash
# Crear un volumen
docker volume create datos-db

# Usar el volumen en un contenedor
docker run -d --name mongodb \
  --mount src=datos-db,dst=/data/db \
  mongo

```

### 8.3 Transferencia de archivos

```bash
# Crear un archivo en el host
echo "Contenido de prueba" > archivo-host.txt

# Iniciar un contenedor Ubuntu en segundo plano
docker run -d --name contenedor-archivos ubuntu sleep infinity

# Copiar un archivo desde el host al contenedor
docker cp archivo-host.txt contenedor-archivos:/tmp/

# Verificar que el archivo existe en el contenedor
docker exec contenedor-archivos ls -l /tmp/

# Modificar el archivo dentro del contenedor
docker exec contenedor-archivos bash -c "echo 'Añadido desde el contenedor' >> /tmp/archivo-host.txt"

# Copiar el archivo modificado de vuelta al host
docker cp contenedor-archivos:/tmp/archivo-host.txt archivo-modificado.txt

# Ver el contenido del archivo recuperado
cat archivo-modificado.txt

```

## 9. Trabajando con imágenes

Las imágenes son la base para crear contenedores y constituyen la solución de Docker para los desafíos de construcción y distribución.

### 9.1 Comandos básicos para imágenes

```bash
# Listar imágenes locales
docker images

# Descargar una imagen
docker pull ubuntu:20.04

# Eliminar una imagen
docker rmi <ID o nombre>

```

### 9.2 Docker Hub

[Docker Hub](https://hub.docker.com/) es el repositorio público oficial de imágenes Docker. Contiene miles de imágenes oficiales y de la comunidad.

### 9.3 Creación de imágenes personalizadas

Crear un archivo `Dockerfile`:

```
# Imagen base
FROM ubuntu:latest

# Instalar dependencias
RUN apt-get update && apt-get install -y \
    nginx \
    curl

# Copiar archivos de la aplicación
COPY ./app /var/www/html

# Puerto que expondrá el contenedor
EXPOSE 80

# Comando que se ejecutará al iniciar el contenedor
CMD ["nginx", "-g", "daemon off;"]

```

Construir la imagen:

```bash
docker build -t mi-aplicacion:1.0 .

```

### 9.4 Sistema de capas

Las imágenes Docker utilizan un sistema de capas superpuestas:

1. Cada instrucción en el Dockerfile crea una nueva capa.
2. Las capas son inmutables y pueden ser compartidas entre imágenes.
3. Cuando un contenedor se ejecuta, Docker añade una capa de escritura.

### 9.5 Buenas prácticas para Dockerfiles

- Usar imágenes oficiales como base.
- Combinar comandos RUN para reducir el número de capas.
- Eliminar archivos temporales en la misma capa donde se crearon.
- Usar .dockerignore para excluir archivos innecesarios.
- Especificar versiones exactas de las imágenes base.
- Minimizar el número de archivos copiados al contexto de construcción.

## 10. Docker Compose

Docker Compose permite definir y ejecutar aplicaciones multi-contenedor mediante archivos YAML.

### 10.1 Instalación

En sistemas Linux, Docker Compose puede requerir instalación separada:

```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin

```

### 10.2 Estructura del archivo docker-compose.yml

```yaml
# docker-compose.yml
services:
  # Servicio de base de datos PostgreSQL
  db:
    image: postgres:14-alpine
    restart: always
    environment:
      POSTGRES_USER: usuario
      POSTGRES_PASSWORD: contraseña
      POSTGRES_DB: mibasededatos
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - backend

  # Adminer - Interfaz web para administrar la base de datos
  adminer:
    image: adminer:latest
    restart: always
    ports:
      - "8080:8080"
    depends_on:
      - db
    networks:
      - backend

networks:
  backend:

volumes:
  postgres_data:

```

Para usar este ejemplo:

1. Crea un directorio para el proyecto:

```bash
mkdir postgres-ejemplo
cd postgres-ejemplo

```

1. Crea el archivo docker-compose.yml con el contenido anterior
2. Inicia los contenedores:

```bash
docker compose up -d

```

1. Accede a Adminer en tu navegador: http://localhost:8080
    - Sistema: PostgreSQL
    - Servidor: db
    - Usuario: usuario
    - Contraseña: contraseña
    - Base de datos: mibasededatos

Este ejemplo:

- Usa PostgreSQL 14 con Alpine Linux (imagen ligera)
- Configura credenciales de acceso y nombre de base de datos mediante variables de entorno
- Incluye persistencia de datos con un volumen nombrado
- Proporciona una interfaz web Adminer para gestionar la base de datos fácilmente
- Organiza los servicios en una red separada
- Expone el puerto de PostgreSQL para poder conectarte también desde aplicaciones externas

### 10.3 Comandos de Docker Compose

```bash
# Crear e iniciar los contenedores
docker-compose up

# Crear e iniciar los contenedores en segundo plano
docker-compose up -d

# Detener y eliminar los contenedores
docker-compose down

# Detener los contenedores sin eliminarlos
docker-compose stop

# Iniciar contenedores detenidos
docker-compose start

# Ver logs
docker-compose logs

# Reconstruir servicios
docker-compose build

# Ejecutar un comando en un servicio
docker-compose exec <servicio> <comando>

```

## 11. Redes en Docker

Docker crea automáticamente distintas redes para facilitar la comunicación entre contenedores.

### 11.1 Tipos de redes

- **bridge**: Red por defecto para contenedores en un host.
- **host**: Elimina el aislamiento de red entre el contenedor y el host.
- **none**: Desactiva la red del contenedor.
- **overlay**: Conecta múltiples daemons de Docker y permite la comunicación entre swarms.
- **macvlan**: Asigna direcciones MAC a contenedores, haciéndolos aparecer como dispositivos físicos.

### 11.2 Comandos para gestionar redes

```bash
# Listar redes
docker network ls

# Crear una red
docker network create mi-red

# Inspeccionar una red
docker network inspect mi-red

# Conectar un contenedor a una red
docker network connect mi-red mi-contenedor

# Desconectar un contenedor de una red
docker network disconnect mi-red mi-contenedor

# Eliminar una red
docker network rm mi-red

```

## 12. Casos de uso prácticos

### 12.1 Entorno de desarrollo web

```yaml
version: "3.8"
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./src:/usr/share/nginx/html
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - php

  php:
    build: ./php
    volumes:
      - ./src:/var/www/html
    depends_on:
      - db

  db:
    image: mariadb:latest
    environment:
      MYSQL_ROOT_PASSWORD: secreto
      MYSQL_DATABASE: miapp
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:

```

### 12.2 Despliegue de aplicación Django

```yaml
version: "3.8"
services:
  web:
    build: ./
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - ./:/code
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://postgres:postgres@db:5432/postgres

  db:
    image: postgres:13
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=postgres

volumes:
  postgres_data:

```

## 13. Buenas prácticas y consejos

- **Contenedores efímeros**: Diseña contenedores que puedan ser detenidos, eliminados y reemplazados con mínima configuración.
- **Un proceso por contenedor**: Cada contenedor debe tener una única responsabilidad.
- **Optimización de imágenes**: Usa imágenes ligeras como Alpine cuando sea posible.
- **Gestión de secretos**: Nunca almacenes contraseñas o claves API en imágenes.
- **Limitar recursos**: Usa flags como `-memory` y `-cpus` para evitar que un contenedor consuma demasiados recursos.
- **Etiquetado adecuado**: Usa etiquetas significativas para tus imágenes, evita `latest`.
- **Monitorización**: Implementa herramientas como Prometheus y Grafana para supervisar tus contenedores.

## 14. Recursos adicionales

- [Documentación oficial de Docker](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Play with Docker](https://labs.play-with-docker.com/) - Entorno práctico gratuito
- [Docker GitHub](https://github.com/docker)
- [Docker Roadmap](https://roadmap.sh/docker) - Ruta de aprendizaje

## 15. Glosario de términos

- **Contenedor**: Instancia ejecutable de una imagen.
- **Imagen**: Plantilla de solo lectura para crear contenedores.
- **Dockerfile**: Archivo de texto con instrucciones para construir una imagen.
- **Registry**: Repositorio de imágenes Docker.
- **Tag**: Etiqueta que identifica la versión de una imagen.
- **Layer (Capa)**: Modificación al sistema de archivos que forma parte de una imagen.
- **Volume**: Mecanismo para persistir datos generados por contenedores.
- **Bind Mount**: Forma de montar un directorio del host en un contenedor.
- **Docker Compose**: Herramienta para definir y ejecutar aplicaciones multi-contenedor.
- **Swarm**: Grupo de máquinas ejecutando Docker que actúan como un cluster.