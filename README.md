<div align="center">

# 🐳 docker-apuntes

**Apuntes y ejercicios para aprender Docker desde cero** — del primer `docker run` a Compose con variables de entorno, healthchecks y redes aisladas. Cada carpeta se levanta sola.

![Docker](https://img.shields.io/badge/Docker-29-2496ED?logo=docker&logoColor=white)
![Compose](https://img.shields.io/badge/Compose-v2-2496ED?logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?logo=nginx&logoColor=white)
![MariaDB](https://img.shields.io/badge/MariaDB-003545?logo=mariadb&logoColor=white)
![CachyOS](https://img.shields.io/badge/probado%20en-CachyOS-00A3E0?logo=archlinux&logoColor=white)
![Estado](https://img.shields.io/badge/estado-aprendiendo-f9a825)

</div>

---

## 📋 Índice

- [Ejercicios](#-ejercicios)
- [Ideas clave](#-ideas-clave)
- [Correrlo en local](#-correrlo-en-local)
- [Comandos que más uso](#-comandos-que-más-uso)
- [Trampas con las que me topé](#-trampas-con-las-que-me-topé)
- [Estructura del repositorio](#-estructura-del-repositorio)
- [Herramientas](#-herramientas)
- [Autoría](#-autoría)

## 🧪 Ejercicios

Van en orden de dificultad: cada uno usa lo aprendido en el anterior.

| N° | Carpeta | Qué practica | Nivel |
|---|---|---|---|
| 1 | `01-nginx-volumen` | `docker run`, puertos y *bind mount* (editar la web en vivo) | 🟢 Básico |
| 2 | `02-dockerfile` | Crear una imagen propia con `Dockerfile` | 🟢 Básico |
| 3 | `03-compose-basico` | Compose con MariaDB + Adminer, red interna y volumen nombrado | 🟡 Intermedio |
| 4 | `04-avanzado` | `.env`, healthchecks, `depends_on` con condición y redes aisladas | 🟠 Avanzado |

## 💡 Ideas clave

| | |
|---|---|
| 📦 **Imagen y contenedor** | La imagen es una plantilla de solo lectura; el contenedor es esa imagen en ejecución y es desechable. |
| 💾 **Datos en volúmenes** | Lo que importa no vive en el contenedor. `down` los conserva; `down -v` los borra. |
| 🔎 **Servicios por nombre** | En Compose se llegan por su nombre (`db`), no por `localhost`. |
| 🔌 **Puertos** | En `-p 8080:80` el primero es el de tu PC (el del navegador) y el segundo el del contenedor. |
| 🔒 **Exponer lo mínimo** | Publica con `127.0.0.1:` delante para no abrir el puerto a tu red, y no publiques la base de datos. |

## 🚀 Correrlo en local

Necesitas Docker y el plugin de Compose. En CachyOS / Arch:

```bash
sudo pacman -S docker docker-compose
sudo systemctl enable --now docker
sudo usermod -aG docker $USER   # cerrar sesión y volver a entrar
```

<details>
<summary><b>▶️ 01 · Nginx con volumen</b></summary>

<br>

```bash
cd 01-nginx-volumen
docker run -d --name web -p 8080:80 -v "$PWD":/usr/share/nginx/html:ro nginx
# abrir http://localhost:8080  (editar index.html y recargar)
docker rm -f web
```

</details>

<details>
<summary><b>▶️ 02 · Imagen propia con Dockerfile</b></summary>

<br>

```bash
cd 02-dockerfile
docker build -t mi-web .
docker run -d --name web -p 9000:80 mi-web
# abrir http://localhost:9000
docker rm -f web
```

</details>

<details>
<summary><b>▶️ 03 · Compose básico (MariaDB + Adminer)</b></summary>

<br>

```bash
cd 03-compose-basico
docker compose up -d
# Adminer: http://localhost:8081 · servidor: db · usuario: root
docker compose down        # conserva los datos
```

</details>

<details>
<summary><b>▶️ 04 · Avanzado (.env, healthcheck, redes)</b></summary>

<br>

```bash
cd 04-avanzado
cp .env.example .env       # cambia la clave
docker compose up -d
# Adminer: http://localhost:8082 · servidor: db
docker compose exec intruso ping -c1 db   # falla: no comparte red con la BD
docker compose down -v     # borra también los datos
```

</details>

## ⌨️ Comandos que más uso

| Comando | Para qué |
|---|---|
| `docker ps` / `docker ps -a` | Ver contenedores activos / todos |
| `docker logs -f web` | Seguir los logs |
| `docker exec -it web sh` | Entrar a un contenedor que corre |
| `docker build -t mi-web .` | Construir una imagen |
| `docker compose up -d --build` | Levantar todo (reconstruyendo) |
| `docker compose ps` | Estado de los servicios |
| `docker compose down` | Apagar y borrar contenedores (conserva volúmenes) |
| `docker compose down -v` | ⚠️ Borra también los datos |

## ⚠️ Trampas con las que me topé

- `depends_on` solo espera a que el contenedor **arranque**, no a que el servicio esté listo: usa `healthcheck` + `condition: service_healthy`.
- Las imágenes pesan: revisa `docker images` y limpia con `docker image prune` de vez en cuando.
- Nunca subas el `.env` real (está en `.gitignore`); sube un `.env.example` con valores falsos.
- Una clave escrita a mano en el `compose.yaml` queda en el repo: en el ejemplo 03 es solo de práctica.

## 🗂️ Estructura del repositorio

```text
docker-apuntes/
├── 01-nginx-volumen/    # index.html
├── 02-dockerfile/       # Dockerfile + index.html
├── 03-compose-basico/   # compose.yaml
├── 04-avanzado/         # compose.yaml + .env.example
└── README.md
```

## 🛠️ Herramientas

| Herramienta | Uso |
|---|---|
| [`lazydocker`](https://github.com/jesseduffield/lazydocker) | Interfaz en terminal para ver contenedores, logs y consumo (`sudo pacman -S lazydocker`) |
| `docker compose config` | Valida el YAML y muestra las variables ya sustituidas |

## 👤 Autoría

**Savka Carvajal** — [@savkacarvajal](https://github.com/savkacarvajal)
