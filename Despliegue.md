# Despliegue de PokeDex en Azure Static Web Apps

## Cuenta de Azure for Students

1. Visita [Azure for Students](https://azure.microsoft.com/free/students/) e inicia sesión o solicita la activación de la oferta para estudiantes.
2. Completa la validación institucional que solicite Microsoft y activa la suscripción.
3. Accede al [portal de Azure](https://portal.azure.com/) y verifica que la suscripción esté disponible.
4. Antes de crear recursos, revisa la directiva **Allowed resource deployment regions**. En esta suscripción se mostraron como permitidas `mexicocentral`, `canadacentral`, `eastus2`, `brazilsouth` y `northcentralus`; selecciona una región permitida que también ofrezca Static Web Apps.
5. No guardes contraseñas, tokens ni claves de despliegue en el repositorio.

> Completa o ajusta estos pasos para que describan exactamente cómo activaste tu cuenta y qué región seleccionaste.

## Crear la aplicación

1. En el portal de Azure, selecciona **Create a resource** y busca **Static Web Apps**.
2. Selecciona la suscripción y el grupo de recursos que usarás.
3. Asigna un nombre al recurso, selecciona un plan adecuado para el proyecto académico y elige una región disponible y permitida por la política de la suscripción.
4. En **Deployment details**, selecciona **GitHub**, autoriza la conexión y elige la organización `7axelamg`, el repositorio `pokedex` y la rama `main`.
5. Configura el build:
   - **Build preset:** Angular
   - **App location:** `/`
   - **API location:** vacío
   - **Output location:** `dist/pokedex-angular`
6. Revisa el workflow de GitHub Actions que propone Azure y crea el recurso.
7. En GitHub, abre **Actions** y verifica que el flujo de compilación y despliegue termine correctamente.
8. La aplicación publicada está en <https://ambitious-flower-0e7e0fc0f.2.azurestaticapps.net/>.

## Configuración de rutas y encabezados

El archivo `src/staticwebapp.config.json` contiene los encabezados HTTP y las reglas de navegación de Azure Static Web Apps. `angular.json` incluye ese archivo en los assets del build para que Azure lo reciba en la raíz del sitio publicado.

La ruta de imágenes de producción es `/assets/images`, porque la aplicación se publica desde la raíz del dominio. Azure usa `navigationFallback` para devolver `index.html` al abrir o recargar rutas internas de Angular.

Los encabezados configurados son:

- **Content-Security-Policy (CSP):** limita la carga de scripts, estilos, imágenes, fuentes y conexiones a los orígenes requeridos por la app y PokéAPI.
- **Strict-Transport-Security (HSTS):** indica al navegador que use HTTPS durante el periodo especificado.
- **X-Content-Type-Options:** evita la interpretación de un archivo con un tipo de contenido diferente al declarado.
- **X-Frame-Options:** impide que otro sitio incruste la aplicación en un frame.
- **Referrer-Policy:** controla qué información de referencia se envía al navegar a otros sitios.
- **Permissions-Policy:** deshabilita permisos de cámara, micrófono y geolocalización que esta aplicación no necesita.

La política CSP permite estilos inline porque Angular puede insertar estilos de componentes en tiempo de ejecución, pero no permite scripts inline. Si el navegador reporta que se bloquea un recurso legítimo, revisa la consola y ajusta solo la directiva CSP necesaria.

## Verificación funcional y de encabezados

1. Abre la URL pública y comprueba la página principal, el listado y los detalles de Pokémon.
2. Confirma que las imágenes cargan y que al recargar directamente una ruta de detalle no aparece un 404.
3. Revisa la consola del navegador por errores de red o violaciones CSP.
4. Comprueba los encabezados desde PowerShell:

   ```powershell
   curl.exe -I https://ambitious-flower-0e7e0fc0f.2.azurestaticapps.net/
   ```

   La respuesta debe incluir `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy` y `Permissions-Policy`.

## Análisis con SecurityHeaders.com

1. Abre [SecurityHeaders.com](https://securityheaders.com/).
2. Analiza `https://ambitious-flower-0e7e0fc0f.2.azurestaticapps.net/`.
3. Revisa las observaciones y corrige los encabezados si hace falta.
4. Después de que Azure despliegue la configuración, ejecuta el análisis nuevamente y registra el resultado real:

   - Fecha del análisis: **[24 Oct 2026 23:10:33 UTC]**
   - Calificación obtenida: **[C]**
   - Evidencia o enlace al resultado: **[(image.png)]**

## Ejecución local

Desde la carpeta del proyecto:

```powershell
npm ci
npm start
```

La app normalmente queda disponible en `http://localhost:4200/`. Para compilar la versión de producción:

```powershell
npm run build
```
