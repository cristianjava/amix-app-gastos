# Amix Gastos

App web instalable (PWA) para registrar gastos mensuales por tarjeta débito o crédito.
No depende de Claude ni de ningún servidor propio: los datos quedan guardados en el celular.

## Archivos

```
index.html             la app completa
manifest.webmanifest   nombre, ícono y colores para instalarla
sw.js                  service worker: permite abrirla sin internet
icons/                 íconos de la app
```

## Requisito importante: HTTPS

Para que el celular la deje instalar y funcione sin internet, la app tiene que servirse por **HTTPS**
(o desde `localhost` en pruebas). Abrir `index.html` directamente como archivo no sirve para instalarla.

## Opción 1: GitHub Pages (gratis, la más fácil)

1. Crea un repositorio nuevo en GitHub, por ejemplo `amix-gastos`.
2. Sube todos los archivos de esta carpeta a la raíz del repositorio:
   ```bash
   git init
   git add .
   git commit -m "Amix Gastos"
   git branch -M main
   git remote add origin https://github.com/<tu-usuario>/amix-gastos.git
   git push -u origin main
   ```
3. En GitHub: **Settings › Pages › Build and deployment › Source: Deploy from a branch**, rama `main`, carpeta `/ (root)`.
4. En uno o dos minutos queda en `https://<tu-usuario>.github.io/amix-gastos/`.

Netlify o Cloudflare Pages funcionan igual: arrastras la carpeta y te dan una URL con HTTPS.

## Opción 2: tu Raspberry Pi con Docker

```bash
docker run -d --name amix-gastos --restart unless-stopped \
  -p 8080:80 -v "$PWD":/usr/share/nginx/html:ro nginx:alpine
```

Para que el celular la instale necesitas HTTPS. Opciones:
- **Cloudflare Tunnel** (`cloudflared`): te da un dominio con HTTPS sin abrir puertos del router.
- Un dominio propio con **Caddy** o nginx + Let's Encrypt.

## Instalar en el celular

- **iPhone (Safari):** abre la URL › botón Compartir › *Agregar a pantalla de inicio*.
- **Android (Chrome):** abre la URL › menú ⋮ › *Instalar app* o *Agregar a la pantalla principal*.

Después de instalada se abre a pantalla completa, como una app normal, y funciona sin internet.

## Tus datos

- Se guardan en el almacenamiento del navegador de **cada** dispositivo. No se sincronizan entre el celular y el computador.
- Si borras la app o los datos del navegador, se borran los gastos. Usa **Tarjetas › Respaldo**:
  - *Exportar a Excel (CSV)*: todos los gastos en una tabla.
  - *Guardar respaldo*: archivo `.json` con tarjetas y gastos.
  - *Restaurar respaldo*: carga un `.json` (sirve también para pasar los datos a otro celular). No duplica registros.

## Actualizar la app

Cuando cambies `index.html`, sube el número en `const VERSION = "amix-gastos-v1"` dentro de `sw.js`
(por ejemplo a `v2`) para que los celulares instalados descarguen la versión nueva.

## Siguiente paso posible: app nativa o con sincronización

- **APK de Android / app de iOS:** envolver esta misma carpeta con [Capacitor](https://capacitorjs.com)
  (`npx cap add android`). Para iPhone se necesita un Mac con Xcode y cuenta de Apple Developer.
- **Sincronizar entre dispositivos:** agregar un backend propio (por ejemplo Spring Boot + PostgreSQL en la Raspberry)
  y que la app guarde ahí en lugar de en el navegador.
