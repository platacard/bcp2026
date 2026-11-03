# BCP Training 2026 · Banco Plata

Curso bilingüe (ES/EN) del Plan de Continuidad de Negocio, publicado en GitHub Pages.
Es un solo archivo, `index.html`, sin dependencias externas.

## Cómo funciona el acceso

1. El colaborador abre el enlace del curso.
2. Si su navegador ya tiene sesión con una cuenta de Banco Plata (@bancoplata.mx, @plata.work o @dif.tech), el curso abre directo.
3. Si no la tiene, se abre automáticamente la pantalla de inicio de sesión de Google y, al terminar, regresa al curso.
4. El token de Google se valida en el servidor (Apps Script del tracker). Al aprobar la evaluación, el resultado se registra en Google Sheets y el certificado se envía al correo verificado.

## Puesta en marcha

### 1. Publicar en GitHub Pages
1. Crea el repositorio y sube `index.html` y `.nojekyll` a la rama `main`.
2. *Settings › Pages › Build and deployment*: Source = **Deploy from a branch**, Branch = `main` / `(root)`.
3. La URL queda como `https://<organización>.github.io/<repositorio>/`. Anótala tal cual, con la diagonal final.

### 2. Crear el cliente OAuth en Google Cloud
1. En console.cloud.google.com, con la cuenta de Workspace, crea o elige un proyecto.
2. *APIs y servicios › Pantalla de consentimiento de OAuth*: tipo de usuario **Interno**. Así solo pueden iniciar sesión cuentas del Workspace de Banco Plata.
3. *Credenciales › Crear credenciales › ID de cliente de OAuth* › Tipo: **Aplicación web**.
   - Orígenes autorizados de JavaScript: `https://<organización>.github.io`
   - URI de redireccionamiento autorizados: `https://<organización>.github.io/<repositorio>/`
4. Copia el **ID de cliente** (termina en `.apps.googleusercontent.com`).

### 3. Actualizar el Apps Script del tracker
1. En el tracker: *Extensiones › Apps Script*. Reemplaza `Código.gs` con la versión nueva y guarda.
2. Ejecuta la función `configurar` y autoriza los permisos (incluye conexiones externas, para validar el token de Google).
3. En la hoja *Config*, pega el ID de cliente en `OAUTH_CLIENT_ID` (la fila se crea sola al ejecutar `configurar`).
4. *Implementar › Nueva implementación › App web*: Ejecutar como **Yo** · Quién tiene acceso: **Cualquier persona**. Copia la URL que termina en `/exec`.
   - "Cualquier persona" es necesario para que GitHub Pages pueda llamar al script. La identidad no depende de eso: el script solo acepta tokens de Google válidos de los tres dominios.

### 4. Configurar `index.html`
Al inicio del archivo, en el bloque `window.BCP_CONFIG`, llena:

```js
API_URL: 'https://script.google.com/macros/s/…/exec',
OAUTH_CLIENT_ID: '….apps.googleusercontent.com',
```

Haz commit. GitHub Pages se actualiza en un par de minutos.

### 5. Probar
- En una ventana de incógnito: debe abrir el inicio de sesión de Google y luego el curso.
- En una ventana con sesión de Banco Plata: el curso debe abrir directo.
- Para revisar contenido sin iniciar sesión: agrega `?preview=1` a la URL; la evaluación queda bloqueada.

## Notas
- **Visibilidad:** GitHub Pages es público aunque el repositorio sea privado, salvo en GitHub Enterprise Cloud con Pages privado. La certificación sí queda protegida por el inicio de sesión de Google, pero el contenido del curso puede leerse en el código fuente.
- **Dominio propio (opcional):** si usas uno, por ejemplo `capacitacion.bancoplata.mx`, agrégalo también en los orígenes y URIs de redireccionamiento del cliente OAuth.
- **Sesión:** dura 8 horas. Si vence antes de enviar la evaluación, se renueva sola sin perder el avance.
