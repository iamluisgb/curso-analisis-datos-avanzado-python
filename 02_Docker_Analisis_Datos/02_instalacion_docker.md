## Cómo instalar Docker en Windows

### Requisitos previos:

1. Windows 10 o 11 — versión Pro, Enterprise o Education (con Hyper-V habilitado)
    - Para Windows Home funciona también, pero usa WSL2 (Windows Subsystem for Linux).
2. Virtualización activada en la BIOS.

---

## Pasos:

### 1. Descargar Docker Desktop

- Ir a: https://www.docker.com/products/docker-desktop/
- Descargar la versión para Windows.

---

### 2. Instalar Docker Desktop

- Ejecutar el instalador `.exe` descargado.
- Seguir los pasos de instalación:
    - Aceptar términos.
    - Elegir si quieres usar WSL 2 (recomendado) o Hyper-V.
    - Reiniciar el equipo si lo solicita.

---

### 3. Configurar WSL 2 (si es necesario)

Docker Desktop te guiará si te falta WSL 2, pero te dejo los pasos:

### Instalar WSL 2:

```powershell
wsl --install

```

### Verificar versión instalada:

```powershell
wsl --list --verbose

```

### Establecer WSL 2 por defecto:

```powershell
wsl --set-default-version 2

```

---

### 4. Verificar instalación

Abre una terminal (PowerShell o CMD) y ejecuta:

```powershell
docker --version

```

Resultado esperado:

```
Docker version XX.XX.X, build XXXXXXX

```

---

### 5. Probar Docker

Lanza un contenedor de prueba:

```powershell
docker run hello-world

```

Si ves el mensaje:

> Hello from Docker!

La instalación se ha ejecutado correctamente.