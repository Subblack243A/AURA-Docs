# AURA Backend - API de Bienestar Emocional

Este es el backend de **AURA**, una plataforma de bienestar emocional que utiliza Inteligencia Artificial para el análisis de sentimientos y reconocimiento facial. Construido con **Django Rest Framework** y **DeepFace**, alojado en el siguiente repositorio [AURA-Back-End](https://github.com/Subblack243A/AURA-Back-End), este repositorio es privado y solo se tendrá acceso a él mediante invitación.

## 🚀 Tecnologías Principales

- **Django & DRF**: Framework principal para la API REST.
- **PostgreSQL + pgvector**: Base de datos relacional con soporte para vectores de alta dimensión (embeddings faciales).
- **DeepFace (Refactored)**: Motor de IA optimizado para 7 niveles emocionales (Felicidad, Tristeza, Desagrado, Ira, Sorpresa, Miedo y Neutral).
- **Dynamic Packaging (io/zipfile)**: Sistema de empaquetado en memoria para la distribución segura de agentes locales.
- **Docker & Docker Compose**: Para una orquestación y despliegue consistentes.

## 📂 Estructura del Proyecto

```text
api/
├── models/tables/      # Definición de modelos de base de datos
├── serializers/        # Transformación de datos (JSON <-> Modelos)
├── services/           # Lógica de negocio pesada (DeepFaceService)
├── views/              # Endpoints de la API
├── urls.py             # Definición de rutas
└── migrations/         # Historial de cambios en la base de datos
AuraBackend/            # Configuración del proyecto Django (settings.py)
manage.py               # Herramienta de gestión de Django
Dockerfile              # Configuración de imagen de contenedor
docker-compose.yml      # Definición de servicios (Backend + DB)
```

## 📊 Modelos de Datos Relevantes

- **UserModel**: Extiende el usuario base de Django para incluir un `VectorField` de 512-d, campos académicos y `last_agent_ping` para monitorear el estado del agente local.
- **RecognitionModel**: Almacena resultados de análisis automático y manual (IDs 1-7).
- **EmotionRegisterModel**: Registros puntuales de estado de ánimo con control de ráfagas (antispam).
- **SurveyModel & SurveyResultModel**: Gestión de tests MBI-SS y diagnósticos de burnout.
- **DictionaryModels**: Tablas maestras para roles, programas, facultades y emociones (Felicidad -> ID 1).

## 🔌 Infraestructura del Agente Biométrico

El backend actúa como servidor central de control para el **AURA Agent** (cliente local):

1.  **Distribución Dinámica** (`/api/biometric/download-agent/`):
    - Genera archivos ZIP al vuelo sin persistencia en disco.
    - Autoinyecta el token de sesión del usuario en un `config.json` único.
    - Sirve el ejecutable `emotion_agent.exe` como un blob binario protegido por autenticación.

2.  **Monitoreo de Estado (Heartbeat)**:
    - Endpoint `/api/biometric/ping/` diseñado para recibir señales de vida cada N minutos.
    - Permite al frontend saber si el estudiante está siendo monitoreado activamente fuera del navegador.

3.  **Captura Remota**:
    - Recepción y procesamiento de imágenes capturadas por el agente local.
    - Categorización automática de imágenes en el dataset basándose en la emoción dominante detectada.

## 🛠️ Requerimientos y Ejecución

### Requisitos Previos

- **Docker** y **Docker Compose** instalados.
- Cámara web funcional (para las pruebas con el frontend).

### Pasos para Ejecutar

1.  **Configurar Variables de Entorno**: Crea un archivo `.env` en la raíz basado en los valores de `settings.py` (DB_NAME, DB_USER, etc.).
2.  **Iniciar Servicios**:
    ```bash
    docker-compose up --build
    ```
3.  **Aplicar Migraciones**:
    ```bash
    docker-compose exec aura_backend python manage.py migrate
    ```

## 🧠 Flujo de Autenticación Facial

1.  El usuario envía sus credenciales y una imagen.
2.  Si el usuario no tiene rostro registrado, el sistema genera un **embedding** y lo guarda en Postgres.
3.  Si ya existe un registro, se compara el nuevo rostro con el guardado usando **distancia del coseno**.
4.  Tras una verificación exitosa, se procede al **análisis de emociones** y se guarda la captura en el dataset categorizado.
