# Guía de Acceso a Swagger UI (Documentación de API)

Swagger (a través de `drf-spectacular`) está configurado en este proyecto para auto-generar y servir la documentación interactiva de la API.

## ¿Cómo acceder localmente?

Si estás ejecutando el entorno con Docker (en la carpeta `AURA-structure`), hemos expuesto el puerto **8000** del backend exclusivamente para propósitos de desarrollo (para poder evitar los proxies cuando sea necesario probar directamente). 

Puedes acceder a la interfaz gráfica de Swagger UI en tu navegador ingresando a:

👉 **[http://localhost:8000/api/schema/swagger-ui/](http://localhost:8000/api/schema/swagger-ui/)**

*Nota: También puedes visualizar la documentación en formato ReDoc ingresando a `http://localhost:8000/api/schema/redoc/`.*

## ¿Cómo probar los endpoints en Swagger?

1. Abre la URL en tu navegador.
2. Verás una lista con todos los endpoints disponibles agrupados (por ejemplo, `/api/biometric/...`).
3. Haz clic en un endpoint para expandirlo.
4. Presiona el botón **"Try it out"** (Probarlo).
5. Rellena los parámetros o el cuerpo de la petición (JSON) si te lo solicita.
6. Haz clic en **"Execute"** para enviar la petición real al backend y ver la respuesta que devuelve en tiempo real. 

### Autenticación en Swagger
Si alguno de tus endpoints requiere un Token (JWT, Bearer, etc.), puedes hacer clic en el botón **"Authorize"** (generalmente ubicado en la parte superior derecha de la página de Swagger) y colocar tu credencial para que todas tus pruebas la incluyan automáticamente.
