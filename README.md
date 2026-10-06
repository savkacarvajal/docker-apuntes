# Apuntes de Docker

Ejercicios que hice para aprender Docker, en orden. Cada carpeta se puede levantar sola.
Probado en CachyOS con Docker 29 y Compose v2.

| Carpeta | Qué practica |
|---|---|
| `01-nginx-volumen` | `docker run`, puertos y *bind mount* (editar la web en vivo) |
| `02-dockerfile` | Crear una imagen propia con `Dockerfile` |
| `03-compose-basico` | Compose con MariaDB + Adminer, red interna y volumen nombrado |
| `04-avanzado` | `.env`, healthchecks, `depends_on` con condición y redes aisladas |

## Ideas clave

- **Imagen** = plantilla de solo lectura. **Contenedor** = esa imagen en ejecución (desechable).
- **Los datos viven en volúmenes**, no en el contenedor. `docker compose down` los conserva; `down -v` los borra.
- En Compose los servicios se encuentran por **nombre** (`db`), no por `localhost`.
- `-p 8080:80` → el primero es el puerto de tu PC (el del navegador), el segundo el del contenedor.
- Publica con `127.0.0.1:` delante (`127.0.0.1:8081:8080`) para no abrir el puerto a tu red.
- Una base de datos no necesita `ports`: solo debe verla el servicio que la usa.

## Comandos que más uso

```bash
docker run -d --name web -p 8080:80 -v ./sitio:/usr/share/nginx/html:ro nginx
docker ps                       # activos (-a: todos)
docker logs -f web
docker exec -it web sh
docker build -t mi-web .
docker compose up -d --build
docker compose ps
docker compose down             # conserva volúmenes
docker compose down -v          # BORRA también los datos
```

## Trampas con las que me topé

- `depends_on` solo espera a que el contenedor **arranque**, no a que el servicio esté listo:
  usa `healthcheck` + `condition: service_healthy`.
- Las imágenes pesan: `docker images` y `docker image prune` de vez en cuando.
- Nunca subas el `.env` real (está en `.gitignore`); sube un `.env.example`.

## Interfaz en terminal

[`lazydocker`](https://github.com/jesseduffield/lazydocker) (`sudo pacman -S lazydocker`) muestra
contenedores, logs y consumo sin memorizar comandos.
