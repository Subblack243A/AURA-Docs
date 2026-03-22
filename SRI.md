# Documentación de Seguridad: Subresource Integrity (SRI)

Para cumplir con los estándares de seguridad expuestos por SonarQube (Vulnerabilidad S5725), hemos implementado **SRI** en todos los recursos externos (scripts y estilos) cargados desde CDNs.

## ¿Qué es SRI?
Subresource Integrity es una característica de seguridad que permite al navegador verificar que los archivos que descarga (por ejemplo, desde un CDN) no han sido manipulados maliciosamente. Se basa en proporcionar un hash criptográfico (`sha384`) del archivo esperado.

## ¿Cómo afecta esto a cambios futuros?

> [!WARNING]
> **Es crucial entender que el hash de integridad es estricto.** Si realizas cualquiera de estos cambios en el futuro, el script **dejará de cargar** y la aplicación podría romperse si no actualizas el atributo `integrity`:

1.  **Actualización de Versión**: Si cambias la versión de una librería (ej: de ApexCharts 3.54.1 a 4.0.0), el contenido del archivo cambia y su hash también. El navegador bloqueará la versión 4.0.0 si mantienes el hash de la 3.54.1.
2.  **Cambio de CDN**: Si cambias de `cdnjs` a `jsdelivr` (o viceversa), a veces incluso la misma versión puede tener diferencias mínimas en la minificación o comentarios, lo que invalidará el hash.
3.  **Modificación del archivo**: Si intentas cargar un archivo local pero le pones un hash de una versión remota, fallará.

## Cómo actualizar un hash

Si necesitas actualizar una librería, sigue estos pasos para obtener el nuevo hash:

### Opción 1: Herramientas Online (Recomendado)
Visita [srihash.org](https://srihash.org/), pega la URL del nuevo script y copia el atributo `integrity` generado.

### Opción 2: Línea de comandos (OpenSSL)
Si tienes acceso a una terminal con OpenSSL, puedes generarlo así:
```bash
curl -s URL_DEL_ARCHIVO | openssl dgst -sha384 -binary | openssl base64 -A | xargs -I {} echo "sha384-{}"
```

## Recursos Protegidos Actuales
- **ApexCharts**: Bloqueado a v3.54.1.
- **jsPDF**: v2.5.1.
- **jsPDF-AutoTable**: v3.5.28.
- **Google Fonts**: Conexiones reforzadas con `crossorigin`.

---
*Para más detalles sobre la implementación inicial, consulta el [walkthrough.md](file:///home/subblack/.gemini/antigravity/brain/356ddb6f-1f91-44a9-9f57-fc35b1b41c94/walkthrough.md).*
