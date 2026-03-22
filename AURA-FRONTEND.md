# AURA - Front-End

Aura es una plataforma inteligente diseñada para el monitoreo del bienestar emocional y académico de los estudiantes de la Universidad de Cundinamarca. Utiliza tecnologías de vanguardia como reconocimiento facial y análisis de emociones para ofrecer una experiencia personalizada y segura, este es alojado en el siguiente repositorio [AURA-Front-End](https://github.com/Subblack243A/AURA-Front-End), este repositorio es privado y solo se tendrá acceso a él mediante invitación.

## 🚀 Tecnologías y Frameworks

El frontend de Aura ha sido desarrollado priorizando la velocidad, la ligereza y una experiencia de usuario (UX) premium:

- **Core**: HTML5 Semántico y JavaScript Vanilla (ES6+).
- **Estilos**: Vanilla CSS3 con variables personalizadas, Glassmorphism y animaciones fluidas (Bezier).
- **Iconografía**: Lucide Icons y SVGs personalizados.
- **Tipografía**: Inter (Google Fonts) para máxima legibilidad.
- **Comunicación**: Fetch API para integración con el Backend REST (Django).
- **Biometría**: Integración con servicios de reconocimiento facial (ArcFace 512-d).

## ✨ Funcionalidades Actuales

### 1. Sistema de Autenticación Biométrica

- **Registro Seguro**: Validación estricta de correo institucional (@ucundinamarca.edu.co).
- **Captura de Rostro**: Flujo paso a paso para el registro inicial de rasgos faciales.
- **Login Facial**: Acceso rápido mediante reconocimiento facial mediante DeepFace.
- **Análisis de Emociones**: Detección automática del estado de ánimo durante el inicio de sesión.

### 2. Bienestar Estudiantil

- **Encuesta MBI-SS**: Implementación del _Maslach Burnout Inventory_ para medir el agotamiento académico.
- **Trigger Inteligente**: La encuesta se activa automáticamente cada 7 días para un seguimiento constante.
- **Interfaz Fluida**: Diseño de encuesta moderno, sin distracciones numéricas y con validación en tiempo real.

### 3. Agente Biométrico Local (AURA Agent)

- **Propósito**: Bypass de restricciones del navegador para el monitoreo continuo de emociones sin interrupciones por cambio de pestaña o minimización.
- **Empaquetado Dinámico**: Descarga de un ejecutable `.zip` generado al vuelo que contiene las credenciales seguras del usuario (`config.json`).
- **Ejecución Silenciosa**: Diseñado para correr en segundo plano en Windows, con control desde la bandeja del sistema (System Tray).
- **Sistema de Heartbeat**: El agente envía señales de vida ("pings") periódicas para informar al panel si el monitoreo está activo en tiempo real.

### 4. Panel de Control (Dashboard)

- **Historial de Resultados**: Visualización de encuestas anteriores con diagnóstico automático de burnout.
- **Visualización Emocional**: Gráficos interactivos (ApexCharts) que comparan registros manuales con detecciones faciales automáticas.
- **Estado del Agente**: Indicador visual dinámico (Activo/Desconectado) basado en el último heartbeat recibido.
- **Portal de Descarga**: Acceso directo para que el estudiante gestione e instale su agente local.
- **Diseño Adaptativo**: Interfaz optimizada tanto para escritorio como para dispositivos móviles.

### 5. Panel de Administración (Admin Dashboard)

- **Gestión de Usuarios**: Control total sobre registros de estudiantes, profesionales y otros administradores.
- **Activación de Cuentas**: Sistema de aprobación para usuarios en estado "Pendiente" tras el registro inicial.
- **Métricas Globales**: Visualización de estadísticas agregadas por facultad y programa académico.
- **Auditoría**: Monitoreo de actividad del sistema y registros biométricos globales.

### 6. Panel del Profesional de la Salud

- **Seguimiento Individualizado**: Búsqueda y análisis detallado por estudiante.
- **Historial Clínico Emocional**: Comparativa visual entre el estado de ánimo reportado manualmente y el detectado por la IA.
- **Reportes de Burnout**: Acceso a los resultados del test MBI-SS con interpretación automática de dimensiones (Cansancio, Cinismo, Eficacia).
- **Alertas de Bienestar**: Identificación temprana de estudiantes con tendencias emocionales negativas persistentes.

## 🛠️ Estructura del Proyecto

- `/src/login-register/`: Lógica de autenticación, vistas de login, registro y captura facial.
- `/src/dashboard/`: Componentes del panel principal, gestión de encuestas y portal del agente.
- `/src/schemas/`: Definiciones JSON para formularios dinámicos (MBI-SS).
- `/src/styles.css`: Sistema de diseño global y tokens visuales.
- `/src/app.js`: Orquestador principal de la aplicación y manejo de rutas.
