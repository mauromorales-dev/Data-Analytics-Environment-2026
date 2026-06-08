# 🐧 Data Analytics Environment 2026

Entorno completo de Data Analytics corriendo sobre **Debian Linux** con Docker Compose. Incluye base de datos, capa de transformación, visualización y administración, todo orquestado en una red local.

---

## 🧱 Stack

| Servicio | Tecnología | Puerto | Descripción |
|---|---|---|---|
| Base de datos | PostgreSQL 15 | `5432` | Motor relacional principal |
| Transformaciones | dbt 1.8 | `3001` | Modelado y transformación SQL |
| Visualización | Metabase | `3000` | Dashboards e inteligencia de negocio |
| Administración | pgAdmin 4 | `5050` | Administración visual de PostgreSQL |

---

## 📁 Estructura del proyecto

```
DataAnalyticsEnviroment2026/
├── docker-compose.yml       # Orquestación de servicios
├── Dockerfile.dbt           # Imagen personalizada de dbt
└── README.md
```

> Los proyectos dbt viven fuera del repositorio en `/home/mauro-dev/Documents/projects_dbt`, montados como bind mount en el contenedor.

---

## ⚙️ Requisitos

- Debian Linux (probado en Debian 13)
- Docker Engine
- Docker Compose v2
- VS Code con extensiones de dbt

---

## 🚀 Inicio rápido

### 1. Clona el repositorio

```bash
git clone https://github.com/tu-usuario/DataAnalyticsEnviroment2026.git
cd DataAnalyticsEnviroment2026
```

### 2. Verifica el contexto de Docker

```bash
docker context use default
docker context show  # debe decir "default"
```

### 3. Levanta el entorno

```bash
docker compose up -d
```

### 4. Verifica que todos los servicios estén corriendo

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

---

## 🔌 Acceso a los servicios

| Servicio | URL | Credenciales |
|---|---|---|
| Metabase | http://localhost:3000 | Configurar en primer acceso |
| pgAdmin | http://localhost:5050 | `admin@tudominio.com` / ver `.env` |
| dbt docs | http://localhost:3001 | — |
| PostgreSQL | `localhost:5432` | Ver variables de entorno |

---

## 🛠️ Configuración de dbt

El archivo `profiles.yml` de dbt debe estar en `~/.dbt/profiles.yml`:

```yaml
my_project:
  target: dev
  outputs:
    dev:
      type: postgres
      host: postgres        # nombre del contenedor en la red Docker
      port: 5432
      user: tu_usuario
      password: "tu_password"
      dbname: analytics_db
      schema: public
      threads: 4
```

### Verificar conexión

```bash
docker exec -it dbt bash
cd /usr/app/dbt_projects/my_project
dbt debug
```

### Correr modelos

```bash
dbt run
dbt test
```

---

## 🌐 Red Docker

Los servicios se comunican a través de una red bridge interna llamada `data-network`. Desde el host se accede por `localhost` con los puertos mapeados.

```
Host (Debian)
└── docker network: data-network (bridge)
    ├── postgres:5432
    ├── dbt:80
    ├── metabase:3000
    └── pgadmin:80
```

---

## 📦 Volúmenes persistentes

| Volumen | Contenido |
|---|---|
| `postgres-data` | Datos de PostgreSQL |
| `dbt-code` | Código del proyecto dbt |
| `metabase-data` | Configuración y dashboards de Metabase |
| `pgadmin-data` | Configuración de pgAdmin |

---

## 🧹 Comandos útiles

```bash
# Bajar todos los servicios
docker compose down

# Ver logs de un servicio
docker compose logs -f metabase

# Entrar al contenedor de dbt
docker exec -it dbt bash

# Reiniciar un servicio
docker compose restart postgres
```

---

## 📌 Notas importantes

- Asegúrate de usar el contexto `default` de Docker y no `desktop-linux` para evitar contenedores duplicados.
- dbt usa `host: postgres` (nombre del contenedor) dentro de Docker. Desde el host usa `localhost`.
- Si hay conflicto de puertos, revisa que ningún otro servicio use los puertos `3000`, `3001`, `5432` o `5050`.

---

## 👤 Autor

**Mauro** — [@mauro-dev](https://github.com/mauro-dev)

#DataEngineering #dbt #Debian #Linux #Docker #PostgreSQL #Metabase #AnalyticsEngineering
