# Servidor de Minecraft con Docker

Este proyecto configura y ejecuta un servidor de Minecraft utilizando Docker y `docker-compose`. Incluye soporte para modpacks de CurseForge y una interfaz web para gestionar archivos con `filebrowser`.

## Requisitos

- Docker
- Docker Compose

## Configuración

### Archivo `docker-compose.yml`

El archivo `docker-compose.yml` define los servicios necesarios para ejecutar el servidor de Minecraft y `filebrowser`.

```yaml
services:
  mc:
    image: itzg/minecraft-server:java8
    tty: true
    stdin_open: true
    ports:
      - "25565:25565"
    environment:
      EULA: "true"
      TYPE: CURSEFORGE
      VERSION: "1.16.5"
      CF_SERVER_MOD: "/modpacks/serverpack9112.zip"
      INIT_MEMORY: 4G
      MAX_MEMORY: 6G
      TZ: America/Santiago
      ENABLE_ROLLING_LOGS: "true"
      EXEC_DIRECTLY: "true"
      STOP_SERVER_ANNOUNCE_DELAY: 60
      MOTD: "TulaCraft"
      OPS: "ketbome"
      DIFFICULTY: "hard"
      MAX_PLAYERS: "10"
      ENABLE_COMMAND_BLOCK: "true"
      VIEW_DISTANCE: "6"
      SIMULATION_DISTANCE: "4"
      PVP: "true"
      ALLOW_FLIGHT: "true"
      PLAYER_IDLE_TIMEOUT: "60"
      ONLINE_MODE: "true"
    volumes:
      - ./mc-data:/data
      - ./modpacks:/modpacks:ro

  init-filebrowser:
    image: filebrowser/filebrowser
    entrypoint: sh -c
    command:
      - "chown -R 1000: /database"
    restart: no
    volumes:
      - ./filebrowser-db:/database

  filebrowser:
    image: filebrowser/filebrowser
    depends_on:
      init-filebrowser:
        condition: service_completed_successfully
    user: "1000:1000"
    environment:
      FB_DATABASE: /database/filebrowser.db
    volumes:
      - ./mc-data:/srv
      - ./filebrowser-db:/database
    ports:
      - "25580:80"

volumes:
  mc-data: {}
  filebrowser-db: {}
```

### Directorios

- `mc-data`: Contiene los datos del servidor de Minecraft.
- `modpacks`: Contiene los archivos `modpack.zip` para CurseForge.
- `filebrowser-db`: Contiene la base de datos de `filebrowser`.

## Instrucciones

1. Clona el repositorio y navega al directorio del proyecto:

```bash
git clone <URL_DEL_REPOSITORIO>
cd <NOMBRE_DEL_DIRECTORIO>
```

2. Crea los directorios necesarios:

```bash
mkdir mc-data modpacks filebrowser-db
```

3. Coloca tu archivo `modpack.zip` en el directorio `modpacks`.

4. Inicia los servicios de Docker:

```bash
docker-compose up -d
```

5. Accede a `filebrowser` en tu navegador web en `http://localhost:25580` con las credenciales por defecto:

- Usuario: admin
- Contrasenia: admin

## Variables de Entorno

- `EULA`: Debe estar establecido en "true" para aceptar el EULA de Minecraft.
- `TYPE`: Tipo de servidor (e.g., VANILLA, FORGE, CURSEFORGE).
- `VERSION`: Versión del servidor de Minecraft.
- `CF_SERVER_MOD`: Ruta al archivo modpack.zip para CurseForge.
- `INIT_MEMORY`: Memoria inicial asignada al servidor.
- `MAX_MEMORY`: Memoria máxima asignada al servidor.
- `TZ`: Zona horaria del servidor.
- `MOTD`: Mensaje del día del servidor.
- `OPS`: Lista de operadores del servidor.
- `DIFFICULTY`: Dificultad del servidor.
- `MAX_PLAYERS`: Número máximo de jugadores.
- `ENABLE_COMMAND_BLOCK`: Habilitar bloques de comandos.
- `VIEW_DISTANCE`: Distancia de visión.
- `SIMULATION_DISTANCE`: Distancia de simulación.
- `PVP`: Habilitar PVP.
- `ALLOW_FLIGHT`: Permitir vuelo.
- `PLAYER_IDLE_TIMEOUT`: Tiempo de espera para jugadores inactivos.
- `ONLINE_MODE`: Modo en línea.

## Versiones de Java Disponibles

| Tag                | Versión de Java | Linux   | Tipo de JVM       | Arquitectura              |
|--------------------|-----------------|---------|-------------------|---------------------------|
| `latest`           | 21              | Ubuntu  | Hotspot           | amd64, arm64              |
| `stable`           | 21              | Ubuntu  | Hotspot           | amd64, arm64              |
| `java21`           | 21              | Ubuntu  | Hotspot           | amd64, arm64              |
| `java21-jdk`       | 21              | Ubuntu  | Hotspot+JDK       | amd64, arm64              |
| `java21-alpine`    | 21              | Alpine  | Hotspot           | amd64, arm64              |
| `java21-graalvm`   | 21              | Oracle  | Oracle GraalVM    | amd64, arm64              |
| `java17`           | 17              | Ubuntu  | Hotspot           | amd64, arm64, armv7       |
| `java17-graalvm`   | 17              | Oracle  | Oracle GraalVM    | amd64, arm64              |
| `java17-alpine`    | 17              | Alpine  | Hotspot           | amd64                     |
| `java11`           | 11              | Ubuntu  | Hotspot           | amd64, arm64, armv7       |
| `java8`            | 8               | Ubuntu  | Hotspot           | amd64, arm64, armv7       |
| `java8-jdk`        | 8               | Ubuntu  | Hotspot+JDK       | amd64                     |
| `java8-openj9`     | 8               | Debian  | OpenJ9            | amd64                     |
| `java8-graalvm-ce` | 8               | Oracle  | GraalVM CE        | amd64                     |