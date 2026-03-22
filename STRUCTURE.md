# 🚀 AURA Healthcare — Monolithic Infrastructure

Este repositorio contiene la orquestación unificada de la plataforma **AURA**. Utiliza **Docker Compose** para gestionar el ciclo de vida del frontend, backend, base de datos y servicios de infraestructura en un solo servidor (DigitalOcean Droplet), alojado en el siguiente repositorio [AURA-structure](https://github.com/Subblack243A/AURA-structure).

---

## 🏗️ Arquitectura de Red y Conectividad

La infraestructura utiliza una red aislada llamada `aura_backend_net`. La seguridad se basa en el principio de **mínima exposición**: solo Nginx y Dozzle exponen puertos al exterior, mientras que el resto de los servicios se comunican internamente a través del DNS de Docker.

### 📦 Tabla de Servicios y Puertos

| Servicio     | Contenedor                | Puerto Externo | Puerto Interno | Rol                                  |
| :----------- | :------------------------ | :------------- | :------------- | :----------------------------------- |
| **Nginx**    | `aura_nginx`              | `80`, `443`    | `8080`, `8443` | Puerta de enlace (SSL/Reverse Proxy) |
| **Frontend** | `aura_frontend`           | Ninguno        | `8080`         | Aplicación Client-side               |
| **Backend**  | `aura_backend_standalone` | Ninguno        | `8000`         | API Django REST Framework            |
| **Database** | `aura_db_standalone`      | Ninguno        | `5432`         | PostgreSQL + pgvector                |
| **Certbot**  | `aura_certbot`            | Ninguno        | N/A            | Renovación de certificados SSL       |
| **Dozzle**   | `aura_dozzle_standalone`  | `8888`         | `8080`         | Visor de logs en tiempo real         |

---

## 🔑 Puntos Críticos de la Infraestructura

### 1. Terminación SSL (Nginx + Certbot)

Nginx actúa como el punto de terminación TLS. Utiliza volúmenes compartidos con Certbot para acceder a los certificados de Let's Encrypt.

- **Seguridad:** Los certificados se montan como _Read-Only_ (`:ro`) para evitar modificaciones accidentales desde el contenedor de Nginx.
- **Persistencia:** Se utilizan volúmenes externos para asegurar que los certificados no se pierdan al reiniciar contenedores.

### 2. Aislamiento de Base de Datos

El contenedor `aura_db_standalone` **no tiene puertos expuestos al host**.

- Para conectar herramientas como **DBeaver**, se debe utilizar un **Túnel SSH** a través del puerto 22 del servidor, apuntando al host `db` o `localhost` en el puerto `5432`.
- Posee un `healthcheck` que asegura que el Backend no inicie hasta que la DB esté lista para recibir conexiones.

### 3. Gestión de Logs (Dozzle)

Disponible en el puerto `8888`.

- **Seguridad:** Implementa autenticación mediante el archivo `./users.yml`.
- **Funcionalidad:** Permite monitorear logs en tiempo real de todos los servicios sin necesidad de acceder por SSH constantemente.

### 4. Límites de Recursos (Docker Deploy)

Para evitar que un proceso consuma todos los recursos del Droplet, se han definido límites estrictos:

- **Backend:** Máximo 1.5GB RAM (proceso con mayor carga).
- **DB:** Máximo 1GB RAM.
- **Nginx:** Máximo 128MB RAM.
- **Frontend:** Máximo 256MB RAM.

---

## 🛠️ Comandos Operativos

### Levantar la infraestructura completa

```bash
docker compose up -d --build
```

### Ver logs de un servicio específico

```bash
docker compose logs -f backend
```

### Ejecutar migraciones en el Backend

```bash
docker exec -it aura_backend_standalone python manage.py migrate
```

### Renovación manual de certificados

```bash
docker compose run --rm certbot renew
docker exec aura_nginx nginx -s reload
```

## 📂 Estructura de Directorios Relativos

Para que los contextos de construcción (build) funcionen, los repositorios deben estar organizados de la siguiente forma en el servidor:

```plaintext
/home/user/
├── AURA-structure/        # Repositorio actual (Este README + Docker Compose)
├── AURA-Back-End/         # Repositorio del Backend Django
└── AURA-Front-End/        # Repositorio del Frontend
```

Nota: El archivo .env debe estar presente en la raíz de AURA-structure/ para que el Backend y la DB carguen sus credenciales correctamente.
