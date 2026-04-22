# Manual Técnico Sistema de Información AURA

    Versión: 1.0
    Fecha: 20/04/2026
    Estado: Producción

## 1. Introducción

### 1.1. Propósito del Documento

El propósito de este documento es proporcionar una guía técnica completa para el desarrollo, mantenimiento y operación del Sistema de Información AURA.

El sistema web AURA permite guardar y gestionar información de los estudiantes de la Universidad de Cundinamarca, haciendo uso de tecnologías de reconocimiento facial basadas en redes neuronales profundas.

**Objetivos:**

- Representar los Requerimientos funcionales y no funcionales recolectados durante el planteamiento del sistema.
- Describir en detalle el funcionamiento y mantenimiento de la arquitectura e infraestructura del software, describiendo sus componentes y la interacción de estos.
- Dar a conocer en proceso paso a paso de implementación del sistema en un servidor.

### 1.2. Descripción General del Sistema 

La plataforma web AURA centraliza la gestión de salud mediante la integración de datos biométricos y variables cualitativas suministradas por el usuario. El sistema procesa esta información para generar perfiles integrales de salud, cuyo acceso está restringido exclusivamente a profesionales médicos previamente autorizados por el administrador. El sistema incorpora una lógica de priorización inteligente, destacando automáticamente aquellos perfiles que presenten patrones críticos de agotamiento.

El desarrollo se encuentra fragmentado en tres repositorios privados de GitHub (acceso exclusivo bajo invitación):
- **Estructura e Infraestructura:** [AURA-structure](https://github.com/Subblack243A/AURA-structure)
- **Backend (API):** [AURA-Back-End](https://github.com/Subblack243A/AURA-Back-End)
- **Frontend:** [AURA-Front-End](https://github.com/Subblack243A/AURA-Front-End)

### 1.3. Stack Tecnológico

| Componente | Tecnología | Versión / Notas |
| :--- | :--- | :--- |
| **Backend (API)** | Django REST Framework | 3.16.1 / Python 3.12 |
| **Frontend** | Vanilla JS / HTML5 / CSS3 | ES8+ |
| **Base de Datos** | PostgreSQL(pgvector) | 16 |
| **Recepción de Peticiones** | Nginx | 1.25 / Configurado como Proxy Inverso |
| **Visualización de Logs** | Docklogkeeper | latest |
| **Contenedores** | Docker / Docker Compose | Engine v24+ / Compose v2+ |
| **Reconocimiento Facial** | Deepface512 | 0.0.98 |

## 2. Arquitectura del Sistema

### 2.1. Diagrama de Arquitectura

Para la comunicación entre cliente y servidor se hace uso de peticiones HTTP que se comunican con el servidor a través del puerto 8000. El servidor, a su vez, se comunica internamente con HTTP entre el frontend, el backend y la base de datos, sin embargo el frontend y la base de datos la base de datos no tienen comunicación directa, sino que se comunican a través de la API la cual a su vez se comunica con la base de datos por medio del puerto 5432, la base de datos no es accesible desde internet, únicamente se puede comunicar con el backend y con un gestor de base de datos haciendo uso de una llave SSH.

![Diagrama de Despliegue](img/Diagrama%20de%20Despliegue.jpg)

### 2.2. Patrones de Diseño

Se utiliza un patrón de diseño MVC (Modelo-Vista-Controlador) para la comunicación entre cliente y servidor, donde las peticiones entran desde la vista, luego pasan por el controlador y este es el único que tiene comunicación con la base de datos.

## 3. Documentación de la API (Backend)

La API del sistema AURA está construida con **Django REST Framework (DRF)** y sigue los principios de diseño REST para la comunicación entre el frontend y el servidor de base de datos. Se aloja en el repositorio privado [AURA-Back-End](https://github.com/Subblack243A/AURA-Back-End).

### 3.1. Configuración del Entorno

El backend requiere un entorno de ejecución basado en **Python 3.12+**. La gestión de dependencias se realiza mediante archivos `requirements.txt` en el entorno de contenedores y `Pipfile` para desarrollo local en sistemas externos al contenedor. El motor de inteligencia artificial **DeepFace** ha sido optimizado para detectar 7 niveles emocionales: Felicidad, Tristeza, Desagrado, Ira, Sorpresa, Miedo y Neutral.

**Dependencias Críticas:**
- `django`: Framework web base.
- `djangorestframework`: Herramientas para la creación de la API.
- `deepface`: Motor de reconocimiento facial y análisis emocional.
- `psycopg2-binary` y `pgvector`: Conector de base de datos y soporte para vectores.

### 3.2. Autenticación

El sistema implementa un esquema de **Autenticación por Token** nativo de DRF (`rest_framework.authentication.TokenAuthentication`).

- **Flujo:** El usuario envía sus credenciales al endpoint de login; si son válidas, el servidor retorna un token único.
- **Uso:** Todas las peticiones protegidas deben incluir el token en el encabezado HTTP de la siguiente manera:
  `Authorization: Token <su_token_aqui>`
- **Modelo de Usuario:** Se utiliza un modelo personalizado en `api.UserModel` que extiende `AbstractUser` de Django para incluir campos como el vector facial (`Face`) y el rol del usuario.

### 3.3. Catálogo de Endpoints

La documentación completa, detallada e interactiva de todos los endpoints de la API se encuentra centralizada en **Swagger UI**. Esto permite probar cada operación en tiempo real y conocer los esquemas de entrada y salida (JSON).

Acceso a la documentación interactiva:
**[http://localhost:8000/api/schema/swagger-ui/](http://localhost:8000/api/schema/swagger-ui/)**

**Funcionalidades Especiales de la API:**
- **Distribución Dinámica de Agentes (`/api/biometric/download-agent/`):** Genera archivos ZIP en memoria que autoinyectan el token de sesión del usuario en un archivo `config.json` para el agente local.
- **Sistema de Heartbeat (`/api/biometric/ping/`):** Recibe señales de vida periódicas del agente local para monitorear el estado del estudiante fuera del navegador.
- **Autenticación Facial:** Utiliza **distancia del coseno** para comparar embeddings de 512 dimensiones y verificar la identidad.

| Categoría | Descripción | Funcionalidad Clave |
| :--- | :--- | :--- |
| **Autenticación y Registro** | Gestión de acceso y creación de cuentas. | Verificación vía OTP y flujo de aprobación. |
| **Biometría** | Servicios de reconocimiento y registro facial. | Comparativa de embeddings (512-d). |
| **Emociones** | Captura y análisis de estados anímicos. | Identificación de 7 niveles emocionales. |
| **Encuestas (MBI-SS)** | Evaluación de agotamiento académico. | Trigger automático de recurrencia (7 días). |
| **Reportes** | Generación de métricas y estadísticas. | Visualización para administradores y salud. |
| **Diccionarios** | Tablas maestras de referencia. | Facultades, programas, roles y causas. |
| **Agente Local** | Control y descarga del software externo. | Empaquetado dinámico y Heartbeat (Ping). |

### 3.4. Manejo de Errores

La API utiliza los códigos de estado HTTP estándar para indicar el resultado de las peticiones:

- `200 OK` / `201 Created`: Petición exitosa.
- `400 Bad Request`: Error en los datos de entrada (generalmente errores de validación).
- `401 Unauthorized`: Token faltante o inválido.
- `403 Forbidden`: El usuario no tiene permisos para realizar la acción solicitada.
- `404 Not Found`: El recurso solicitado no existe.
- `500 Internal Server Error`: Error inesperado en el servidor.

Las respuestas de error suelen incluir un cuerpo JSON con el detalle del campo que falló o un mensaje descriptivo de la excepción.

## 4. Documentación del Frontend

El frontend de AURA está desarrollado bajo el estándar de **Single Page Application (SPA)** utilizando **JavaScript Vainilla (ES8+)**. Se aloja en el repositorio privado [AURA-Front-End](https://github.com/Subblack243A/AURA-Front-End).

### 4.1. Estructura de Carpetas

El código fuente se organiza en el directorio `/src`, con la siguiente jerarquía:

| Directorio | Propósito |
| :--- | :--- |
| `admin/` | Lógica y componentes exclusivos para el panel de administración. |
| `dashboard/` | Vistas principales, gestión de encuestas y portal de descarga del agente. |
| `hcpro/` | Módulos para el seguimiento por profesionales de la salud. |
| `login-register/` | Flujos de autenticación, registro y captura de biometría facial. |
| `schemas/` | Definiciones JSON para formularios dinámicos y validaciones. |
| `assets/` | Recursos multimedia (imágenes, iconos y fuentes). |
| `app.js` | Orquestador de rutas y punto de entrada de la SPA. |
| `styles.css` | Sistema de diseño global (Glassmorphism y variables CSS). |

### 4.2. Gestión de Estado

La aplicación utiliza un patrón de **Objeto Global** para gestionar el estado y la navegación:

- El objeto `window.App` actúa como controlador principal, encargándose de renderizar las diferentes vistas basándose en el estado de autenticación y el rol del usuario.
- Los datos de sesión (token, información del usuario) se almacenan en el `localStorage` del navegador a través del módulo `Auth`, permitiendo la persistencia de la sesión.

### 4.3. Integración con la API

La comunicación con el backend se realiza mediante la API nativa `fetch` de JavaScript.

- **Centralización:** Las peticiones están orquestadas principalmente en los servicios de vistas y el módulo de autenticación.
- **Seguridad:** Todas las peticiones a endpoints protegidos adjuntan automáticamente el token de autenticación recuperado del `localStorage`.
- **Configuración:** La URL base de la API se define globalmente en `config.js` (`window.API_BASE_URL = '/api'`), lo que facilita el cambio de entorno entre desarrollo y producción (vía Nginx).
- **Seguridad SRI:** Se implementa *Subresource Integrity* (SRI) en recursos externos críticos como ApexCharts (v3.54.1) y jsPDF (v2.5.1) para evitar la carga de scripts manipulados.
- **Triggers Inteligentes:** La encuesta MBI-SS se activa automáticamente bajo un esquema de recurrencia de 7 días.

### 4.4. Guía de Estilos y Componentes UI

El diseño visual es completamente personalizado y se encuentra definido en el archivo `styles.css`, priorizando una estética premium:

- **Estética:** Uso de **Glassmorphism**, tipografía **Inter** (Google Fonts) e iconografía de **Lucide Icons**.
- **Componentes:** Los elementos de interfaz (botones, tarjetas, modales) se definen de forma semántica y reutilizable mediante clases CSS estándar.
- **Visualización:** Empleo de **ApexCharts** para gráficas interactivas de evolución emocional y académica.
- **Responsividad:** Consultas de medios (Media Queries) integradas para garantizar la usabilidad en dispositivos móviles y de escritorio.

## 5. Base de Datos

El sistema utiliza **PostgreSQL** como motor de base de datos relacional, potenciado con la extensión `pgvector` para el almacenamiento y búsqueda eficiente de vectores de características faciales (embeddings de **512 dimensiones**).

### 5.1. Modelo de Datos

La arquitectura de datos se divide en diccionarios (tablas maestras) y tablas de registro de actividad.

| Tabla | Descripción | Datos Clave |
| :--- | :--- | :--- |
| **UserModel** | Perfil de identidad y acceso. | Email, Rol, Embedding Facial (512-d). |
| **EmotionRegisterModel** | Registro de estados de ánimo. | Emoción detectada, Causa, Intensidad. |
| **SurveyModel** | Resultados del test MBI-SS. | Dimensiones de Burnout, Fecha de registro. |
| **RecognitionModel** | Auditoría de biometría. | Tipo de análisis, Resultado, Imagen capturada. |
| **DictionaryModels** | Catálogos de referencia. | Carreras, Facultades y Jerarquías. |

![Modelo Entidad Relación](img/Diagrama%20MER.png)

### 5.2. Migraciones

La gestión del esquema de la base de datos se realiza mediante el sistema de migraciones nativo de Django. Todas las modificaciones estructurales deben ser rastreadas en la carpeta `api/migrations/` para asegurar la paridad entre los entornos de desarrollo y producción.

## 6. Configuración y Despliegue (DevOps)

El despliegue de AURA está completamente automatizado y containerizado, orquestado desde el repositorio [AURA-structure](https://github.com/Subblack243A/AURA-structure) y diseñado para ser alojado en un **DigitalOcean Droplet**.

### 6.1. Variables de Envorno e Infraestructura de Servidor

El sistema centraliza su configuración en un archivo `.env` ubicado en la raíz. Para una correcta construcción, los repositorios deben estar ubicados al mismo nivel jerárquico dentro del servidor:
1. `AURA-structure/`
2. `AURA-Back-End/`
3. `AURA-Front-End/`

**Red y Seguridad:**
- **Red Aislada:** Todos los contenedores operan sobre la red `aura_backend_net`.
- **Mínima Exposición:** Los contenedores de base de datos y backend no tienen puertos expuestos al exterior. Solo Nginx expone los puertos 80 y 443.
- **Terminación SSL:** Nginx gestiona los certificados de Let's Encrypt, los cuales se montan como volúmenes de solo lectura (`:ro`) por seguridad.

### 6.2. Dockerización y Límites de Recursos

La infraestructura se define mediante **Docker Compose**, definiendo:

| Contenedor | Rol / Responsabilidad | Recurso Límite |
| :--- | :--- | :--- |
| `nginx` | Proxy Inverso, Terminación SSL y Servidor de archivos. | 128MB RAM |
| `aura_backend` | API Django (Gunicorn), lógica de IA y servicios. | 1.5GB RAM |
| `aura_frontend` | Servidor de activos estáticos para la SPA. | 256MB RAM |
| `db` | Almacenamiento PostgreSQL con extensión pgvector. | 1GB RAM |
| `certbot` | Automatización de certificados Let's Encrypt. | N/A |
| `docklogkeeper` | Monitoreo visual de logs en tiempo real (P. 8888). | N/A |

## 7. Pruebas (Testing)

El sistema AURA cuenta con una suite de pruebas automatizadas en el backend para garantizar la integridad de las funciones críticas.

### 7.1. Pruebas Unitarias e Integración

Las pruebas se localizan en el directorio `api/tests/` y cubren los siguientes flujos:
- **Autenticación y Registro:** Verificación de flujos de login, recuperación de contraseña y gestión de códigos OTP.
- **Gestión de Encuestas:** Validación de la lógica de negocio para el cuestionario MBI-SS.
- **Reportes:** Pruebas de integridad de los datos estadísticos generados para administradores.
- **Flujos de Rol:** Casos específicos para estudiantes, profesionales de la salud y administradores.

Las pruebas se ejecutan utilizando el motor de test nativo de Django:
`python manage.py test api.tests`

### 7.2. Cobertura (Coverage)

Se recomienda mantener una cobertura superior al 80% en los módulos de servicios (`api/services`) y vistas (`api/views`), ya que contienen la lógica central del sistema.

![Diagrama de Clase](img/Diagrama%20de%20Clase%20Modelos.jpg)

![Diagrama de Despliegue](img/Diagrama%20de%20Clase.jpg)

## 8. Mantenimiento y Logs

El mantenimiento preventivo y correctivo del sistema se centraliza en la gestión y análisis de logs generados por los contenedores.

- **Monitoreo en Tiempo Real (Docklogkeeper):** Accesible en el puerto `8888` con el link https://api.aurahealthcare.tech:8888. Proporciona una interfaz web para visualizar los logs de salida estándar de todos los servicios de Docker (Nginx, Backend, DB).
- **Logs de Base de Datos:** Los logs de PostgreSQL están persistidos en un volumen mapeado (`../AURA-Back-End/AuraBackend/logs/postgres`) para su auditoría histórica.
- **Acceso Directo:** Es posible consultar los logs de cualquier servicio mediante el comando:
  `docker compose logs -f <nombre_servicio>`

## 9. Glosario Y Referencias

| Término | Definición |
| :--- | :--- |
| **SPA** | Single Page Application; aplicación que se carga una sola vez. |
| **DRF** | Django REST Framework; kit de herramientas para construir APIs. |
| **DeepFace** | Framework de IA para análisis facial y emocional. |
| **pgvector** | Extensión de PostgreSQL para búsqueda de similitud vectorial. |
| **Embedding** | Representación numérica de características (512 dimensiones). |
| **OTP** | One-Time Password para validación de seguridad de doble factor. |
| **SRI** | Subresource Integrity para validar scripts externos por hash. |
| **Heartbeat** | Señales de red periódicas (pings) para verificar actividad. |

