# Guía de Uso: Dozzle (Visor de Logs en Tiempo Real)

Dozzle es una aplicación ligera que te permite ver los logs de tus contenedores Docker en tiempo real directamente desde tu navegador web.

## Acceso

Una vez que tu entorno esté corriendo mediante `docker compose up -d`, puedes acceder a Dozzle a través del navegador:

**URL:** `http://<NOMBRE_DEL_DOMINIO_O_IP_DEL_SERVIDOR>:8888`
_(o `http://localhost:8888` si estás probando en tu máquina local)_

## Autenticación

Cuando intentes ingresar a la URL, el navegador te desplegará una ventana pidiendo usuario y contraseña.

Estas credenciales están definidas de forma segura en el archivo `AURA-structure/users.yml` que ha sido generado por Dozzle.

> **Nota de Seguridad:** Si necesitas cambiar esta contraseña en el futuro, o agregar nuevos usuarios, utiliza la herramienta de terminal de Dozzle:
> `docker run --rm amir20/dozzle generate <tu_usuario> --password <tu_pass> >> users.yml`
> Y luego reinicia el contenedor con `docker compose up -d dozzle`.

## ¿Cómo funciona Dozzle?

1. En la pantalla principal, verás a la izquierda una lista con los nombres de todos tus contenedores activos (ej. `aura_backend_standalone`, `aura_frontend`, etc).
2. Haz clic en cualquiera de ellos para ver los logs en directo (streaming).
3. Puedes pausar los logs, buscar texto dentro de ellos, y vaciarlos de tu vista temporalmente.
