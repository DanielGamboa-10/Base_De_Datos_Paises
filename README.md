# 🌍 Base de Datos de Países y Ciudades del Mundo

[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15_Alpine-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![pgAdmin](https://img.shields.io/badge/pgAdmin-4-326690?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.pgadmin.org/)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg?style=for-the-badge)](https://conventionalcommits.org)

Entorno contenerizado con **PostgreSQL 15** y **pgAdmin 4** gestionado mediante **Docker Compose**, diseñado para el despliegue automático de una base de datos relacional con información geográfica mundial (países, ciudades, idiomas y continentes).

---

## 📋 Tabla de Contenidos

- [✨ Características Principales](#-características-principales)
- [🏛️ Arquitectura del Sistema](#️-arquitectura-del-sistema)
- [📁 Estructura del Proyecto](#-estructura-del-proyecto)
- [🗄️ Modelo de Datos](#️-modelo-de-datos)
- [📦 Requisitos Previos](#-requisitos-previos)
- [🚀 Guía de Inicio Rápido](#-guía-de-inicio-rápido)
  - [1. Clonar el repositorio](#1-clonar-el-repositorio)
  - [2. Configurar variables de entorno](#2-configurar-variables-de-entorno)
  - [3. Levantar los servicios](#3-levantar-los-servicios)
  - [4. Verificar el estado](#4-verificar-el-estado)
- [🔌 Conexión y Administración](#-conexión-y-administración)
  - [🖥️ Acceso vía pgAdmin 4](#️-acceso-vía-pgadmin-4)
  - [💻 Acceso vía Terminal (psql)](#-acceso-vía-terminal-psql)
  - [🌐 Acceso desde Clientes Externos](#-acceso-desde-clientes-externos)
- [🔍 Consultas SQL de Ejemplo](#-consultas-sql-de-ejemplo)
- [🛠️ Comandos de Mantenimiento](#️-comandos-de-mantenimiento)
- [📜 Convenciones de Commits](#-convenciones-de-commits)
- [👥 Autor](#-autor)

---

## ✨ Características Principales

- 🐳 **Despliegue con un solo comando**: Automatización completa de la infraestructura con Docker Compose.
- ⚡ **Inicialización Automática**: Ejecución secuencial de scripts SQL (`/docker-entrypoint-initdb.d`) para creación de tablas y precarga masiva de datos.
- 🖥️ **Panel Web pgAdmin 4**: Interfaz gráfica preconfigurada para administración y ejecución de queries.
- 🔒 **Variables de Entorno Centralizadas**: Configuración segura de credenciales mediante archivo `.env`.
- 💾 **Persistencia de Datos**: Volúmenes de Docker configurados para evitar pérdida de información.

---

## 🏛️ Arquitectura del Sistema

```
+-------------------------------------------------------------+
|                      Host Machine                           |
|                                                             |
|   +-------------------+             +-------------------+   |
|   |   pgAdmin 4       |             |   PostgreSQL 15   |   |
|   |   Port: 8081      |             |   Port: 5433      |   |
|   +---------+---------+             +---------+---------+   |
|             |                                 |             |
|             +----------------+----------------+             |
|                              |                              |
|                      [postgres_net]                         |
|                    (Red Docker Bridge)                      |
|                                                             |
|   Volúmenes:                                                |
|   • postgres_data ──> /var/lib/postgresql/data              |
|   • pgadmin_data  ──> /var/lib/pgadmin                      |
|   • ./init        ──> /docker-entrypoint-initdb.d           |
+-------------------------------------------------------------+
```

---

## 📁 Estructura del Proyecto

```text
Base_Datos_Paises/
├── .env                  # Variables de entorno locales
├── .env.example          # Plantilla de variables de entorno
├── .gitignore            # Exclusiones de control de versiones
├── docker-compose.yml    # Definición de servicios (Postgres y pgAdmin)
├── README.md             # Documentación técnica del proyecto
└── init/                 # Scripts SQL de inicialización automática
    ├── 00-schema.sql     # Definición de esquema DDL y tablas
    ├── city.sql          # Inserción masiva de ciudades
    ├── country.sql       # Inserción masiva de países
    └── countrylanguage.sql # Inserción de idiomas oficiales y porcentajes
```

---

## 🗄️ Modelo de Datos

El esquema relacional consta de 4 entidades principales:

1. 🌍 **`continent`**: Catálogo de continentes con código autoincremental y nombre.
2. 🗺️ **`country`**: Información demográfica, geográfica, política y económica de los países (`code`, `name`, `continent`, `region`, `surfacearea`, `population`, `lifeexpectancy`, `gnp`, `capital`, etc.).
3. 🏙️ **`city`**: Registro de ciudades mundiales (`id`, `name`, `countrycode`, `district`, `population`).
4. 🗣️ **`countrylanguage`**: Idiomas hablados por país (`countrycode`, `language`, `isofficial`, `percentage`).

---

## 📦 Requisitos Previos

Asegúrate de tener instalados los siguientes componentes en tu sistema:

- 🐙 [Git](https://git-scm.com/) (v2.x o superior)
- 🐳 [Docker Engine](https://docs.docker.com/engine/install/) (v20.10 o superior)
- 📦 [Docker Compose](https://docs.docker.com/compose/install/) (v2.x o superior)

---

## 🚀 Guía de Inicio Rápido

### 1. Clonar el repositorio

```bash
git clone https://github.com/DanielGamboa-10/Base_De_Datos_Paises.git
cd Base_De_Datos_Paises
```

### 2. Configurar variables de entorno

Si no tienes el archivo `.env`, puedes crearlo copiando la plantilla:

```bash
# En Linux/macOS
cp .env.example .env

# En Windows (PowerShell)
Copy-Item .env.example .env
```

Contenido de referencia para el archivo `.env`:

```env
# PostgreSQL Configuration
POSTGRES_USER=bkseducate
POSTGRES_PASSWORD=bkseducate2026
POSTGRES_DB=bkddb
POSTGRES_PORT=5433

# pgAdmin Configuration
PGADMIN_DEFAULT_EMAIL=danieljgamboa55@gmail.com
PGADMIN_DEFAULT_PASSWORD=VINICIUSjr22@
PGADMIN_PORT=8081
```

### 3. Levantar los servicios

Ejecuta el siguiente comando para construir e iniciar los contenedores en segundo plano:

```bash
docker compose up -d
```

> 💡 **Nota:** La primera vez que se ejecute, PostgreSQL procesará todos los scripts en `init/` en orden alfabético (`00-schema.sql` -> `city.sql` -> `country.sql` -> `countrylanguage.sql`). Esto tomará unos segundos.

### 4. Verificar el estado

Verifica que ambos contenedores se encuentren en estado `Up`:

```bash
docker compose ps
```

Para ver los logs en tiempo real:

```bash
docker compose logs -f postgres
```

---

## 🔌 Conexión y Administración

### 🖥️ Acceso vía pgAdmin 4

1. Abre tu navegador e ingresa a: **`http://localhost:8081`**
2. Inicia sesión con las credenciales configuradas en `.env`:
   - 📧 **Email**: `danieljgamboa55@gmail.com` (o el valor en `PGADMIN_DEFAULT_EMAIL`)
   - 🔑 **Contraseña**: `VINICIUSjr22@` (o el valor en `PGADMIN_DEFAULT_PASSWORD`)
3. Registrar el servidor PostgreSQL:
   - Haz clic derecho en **Servers** -> **Register** -> **Server...**
   - En la pestaña **General**:
     - **Name**: `PostgreSQL Local` (o el nombre de tu preferencia)
   - En la pestaña **Connection**:
     - **Host name/address**: `postgres` (nombre del servicio en Docker)
     - **Port**: `5432` (puerto interno del contenedor)
     - **Maintenance database**: `bkddb` (o el valor en `POSTGRES_DB`)
     - **Username**: `bkseducate` (o el valor en `POSTGRES_USER`)
     - **Password**: `bkseducate2026` (o el valor en `POSTGRES_PASSWORD`)
   - Haz clic en **Save**.

---

### 💻 Acceso vía Terminal (psql)

Puedes acceder directamente a la consola interactiva de PostgreSQL dentro del contenedor:

```bash
docker exec -it postgres_db psql -U bkseducate -d bkddb
```

---

### 🌐 Acceso desde Clientes Externos

Para conectarte desde herramientas de escritorio como **DBeaver**, **TablePlus**, **DataGrip** o extensiones de **VS Code**:

| Parámetro | Valor |
| :--- | :--- |
| **Host** | `localhost` / `127.0.0.1` |
| **Port** | `5433` (puerto expuesto en el host) |
| **Database** | `bkddb` |
| **User** | `bkseducate` |
| **Password** | `bkseducate2026` |

---

## 🔍 Consultas SQL de Ejemplo

Prueba la carga de datos ejecutando las siguientes consultas en el Query Tool de pgAdmin o en `psql`:

#### 1. 🏆 Top 10 países más poblados:
```sql
SELECT code, name, continent, population, lifeexpectancy 
FROM public.country 
ORDER BY population DESC 
LIMIT 10;
```

#### 2. 🏙️ Ciudades con mayor población por país:
```sql
SELECT c.name AS ciudad, co.name AS pais, c.population 
FROM public.city c
JOIN public.country co ON c.countrycode = co.code
ORDER BY c.population DESC 
LIMIT 10;
```

#### 3. 🗣️ Idiomas oficiales hablados en Sudamérica:
```sql
SELECT DISTINCT cl.language, co.name AS pais
FROM public.countrylanguage cl
JOIN public.country co ON cl.countrycode = co.code
WHERE co.continent = 'South America' AND cl.isofficial = true
ORDER BY cl.language;
```

#### 4. 📊 Total de países y población por continente:
```sql
SELECT continent, COUNT(*) AS total_paises, SUM(population) AS poblacion_total
FROM public.country
GROUP BY continent
ORDER BY poblacion_total DESC;
```

---

## 🛠️ Comandos de Mantenimiento

| Acción | Comando |
| :--- | :--- |
| 🛑 **Detener contenedores** | `docker compose stop` |
| ▶️ **Iniciar contenedores detenidos** | `docker compose start` |
| 🔄 **Reiniciar todos los servicios** | `docker compose restart` |
| 🔻 **Detener y remover contenedores** | `docker compose down` |
| 🧹 **Reiniciar desde cero (borrando volúmenes y datos)** | `docker compose down -v && docker compose up -d` |
| 📜 **Ver logs de todos los servicios** | `docker compose logs -f` |

---

## 📜 Convenciones de Commits

Este repositorio sigue el estándar de [Conventional Commits](https://www.conventionalcommits.org/) enriquecido con [Gitmoji](https://gitmoji.dev/):

| Formato | Ejemplo | Descripción |
| :--- | :--- | :--- |
| `feat:` ✨ | `feat(database): ✨ add world schema and data fixtures` | Nueva característica |
| `fix:` 🐛 | `fix(compose): 🐛 correct port binding conflict` | Corrección de errores |
| `docs:` 📝 | `docs(readme): 📝 add execution and connection guide` | Documentación |
| `chore:` 🔧 | `chore(env): 🔧 add environment variable templates` | Tareas de configuración |
| `style:` 💄 | `style(sql): 💄 format sql queries and indentation` | Mejoras de formato de código |
| `refactor:` ♻️ | `refactor(db): ♻️ optimize table constraint definitions` | Refactorización de código |

---

## 👥 Autor

- **Daniel Gamboa** - [@DanielGamboa-10](https://github.com/DanielGamboa-10)
