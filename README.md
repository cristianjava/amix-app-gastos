# Amix Gastos

App web instalable (PWA) para registrar gastos mensuales por tarjeta débito o crédito y llevar el control de deudas.
No depende de ningún servidor propio: los datos quedan guardados en el celular.

## Funcionalidades

- **Registrar:** tipo de tarjeta (débito / crédito), tarjeta, monto y descripción (obligatoria). La fecha y la hora se ponen solas.
- **Gastos del mes:** filtro por débito / crédito y por tarjeta (si solo hay una, queda seleccionada por defecto).
  Las compras van de la más reciente a la más antigua, con la descripción en negrilla, el día y la hora, y el total del mes al final.
- **Tarjetas:** lista de tarjetas débito y crédito, y respaldo de los datos (exportar CSV, guardar y restaurar respaldo).
- **Deudas:** dos vistas, *Me deben* y *Debo*. Cada una registra valor y descripción, y muestra la lista con fecha y total.

## Archivos

```
index.html             la app completa (HTML, CSS y JavaScript)
manifest.webmanifest   nombre, ícono y colores para instalarla
sw.js                  service worker: permite abrirla sin internet
icons/                 íconos de la app
README.md              este archivo
```

> **Requisito importante: HTTPS.** Para que el celular la deje instalar y funcione sin internet, la app tiene que servirse por HTTPS.
> Abrir `index.html` directamente como archivo no sirve para instalarla. GitHub Pages da HTTPS gratis.

---

## Despliegue en GitHub Pages (paso a paso)

### Requisitos

- Cuenta en [github.com](https://github.com).
- Git instalado (`git --version` en la terminal para comprobarlo).
- El proyecto abierto en IntelliJ (o cualquier editor).

### 1. Crear el repositorio en GitHub

1. Entra a GitHub y toca **New** (o el **+** de arriba a la derecha › **New repository**).
2. **Repository name:** `amix-gastos`.
3. **Visibility:** **Public**. GitHub Pages es gratis solo en repositorios públicos.
   El código queda público, pero tus gastos no, porque se guardan en el celular y nunca se suben a GitHub.
4. **No** marques *Add a README*, *.gitignore* ni *license*: el repositorio debe quedar vacío.
5. Toca **Create repository** y copia la URL que aparece, por ejemplo:
   `https://github.com/<tu-usuario>/amix-gastos.git`

### 2. Subir el proyecto desde IntelliJ

Abre la terminal de IntelliJ (**View › Tool Windows › Terminal**) en la carpeta del proyecto, la que contiene `index.html`:

```bash
git init
git add .
git commit -m "Amix Gastos: primera versión"
git branch -M main
git remote add origin https://github.com/<tu-usuario>/amix-gastos.git
git push -u origin main
```

- Si el proyecto ya era un repositorio Git, omite `git init`.
- Si ya tenía un `origin` configurado, revisa con `git remote -v`. Para cambiarlo usa
  `git remote set-url origin https://github.com/<tu-usuario>/amix-gastos.git`.
- Si al hacer push pide contraseña: GitHub ya no acepta la contraseña de la cuenta. Usa un **Personal Access Token**
  (GitHub › Settings › Developer settings › Personal access tokens) o inicia sesión desde IntelliJ en
  **Settings › Version Control › GitHub › Add Account**.

**Importante:** `index.html` debe quedar en la **raíz** del repositorio, no dentro de una subcarpeta.
En GitHub, al abrir el repositorio, deben verse directamente `index.html`, `sw.js`, `manifest.webmanifest` e `icons/`.

### 3. Activar GitHub Pages

1. En el repositorio de GitHub entra a **Settings** (pestaña de arriba).
2. En el menú de la izquierda toca **Pages**.
3. En **Build and deployment › Source** elige **Deploy from a branch**.
4. En **Branch** elige **main** y la carpeta **/ (root)**. Toca **Save**.
5. Espera 1 o 2 minutos y recarga la página. Arriba aparece:
   **"Your site is live at `https://<tu-usuario>.github.io/amix-gastos/`"**.

El avance del despliegue se ve en la pestaña **Actions** del repositorio (una ejecución llamada *pages build and deployment*).
Cuando sale en verde, la app ya está publicada.

### 4. Comprobar que funciona

Abre `https://<tu-usuario>.github.io/amix-gastos/` en el navegador del computador. Debe cargar la app.
Si sale un error 404, revisa que `index.html` esté en la raíz y que Pages esté apuntando a `main` / `(root)`.

---

## Instalar en el celular

### iPhone (Safari)

1. Abre `https://<tu-usuario>.github.io/amix-gastos/` en **Safari** (no en Chrome ni en otro navegador).
2. Toca el ícono **≡** de la barra de abajo y luego **Compartir**.
3. Toca **Agregar a pantalla de inicio**, escribe "Amix Gastos" y toca **Agregar**.
4. Ábrela una vez con internet desde el ícono nuevo. Después funciona también sin conexión.

### Android (Chrome)

1. Abre la misma dirección en **Chrome**.
2. Toca el menú **⋮** (arriba a la derecha).
3. Toca **Instalar app** o **Agregar a la pantalla principal** y confirma.

La app queda con su ícono propio y se abre a pantalla completa, sin la barra del navegador.

---

## Publicar cambios (actualizar la app)

1. Haz los cambios en `index.html` (o en los demás archivos).
2. En `sw.js` sube el número de versión. Sin este paso los celulares siguen mostrando la versión guardada:
   ```js
   const VERSION = "amix-gastos-v3";   // antes v2
   ```
3. Sube los cambios:
   ```bash
   git add .
   git commit -m "Descripción del cambio"
   git push
   ```
4. Espera 1 o 2 minutos a que GitHub Pages despliegue (pestaña **Actions** en verde).
5. En el celular abre la app **con internet**. Si sigue viéndose la versión anterior, ciérrala por completo y vuelve a abrirla.

Los datos ya registrados no se pierden al actualizar, porque están guardados en el celular y no en el repositorio.

---

## Tus datos

- Se guardan en el almacenamiento del navegador de **cada** dispositivo. No se sincronizan entre el celular y el computador.
- Si borras la app o los datos del navegador, se borran los registros. Usa **Tarjetas › Respaldo**:
  - *Exportar a Excel (CSV):* todos los gastos en una tabla.
  - *Guardar respaldo:* archivo `.json` con tarjetas, gastos y deudas.
  - *Restaurar respaldo:* carga un `.json`. Sirve también para pasar los datos a otro celular y no duplica registros.

## Otras formas de desplegar

- **Netlify o Cloudflare Pages:** arrastras la carpeta del proyecto y te dan una URL con HTTPS.
- **Raspberry Pi con Docker:**
  ```bash
  docker run -d --name amix-gastos --restart unless-stopped \
    -p 8080:80 -v "$PWD":/usr/share/nginx/html:ro nginx:alpine
  ```
  Para instalarla en el celular necesitas HTTPS: usa **Cloudflare Tunnel** (`cloudflared`) o un dominio propio con Caddy o nginx + Let's Encrypt.

## Siguiente paso posible: Play Store o sincronización

- **Android (Play Store):** generar el paquete `.aab` con [PWABuilder](https://www.pwabuilder.com) a partir de la URL publicada.
  La cuenta de desarrollador cuesta US$25 (pago único) y las cuentas personales nuevas deben hacer una prueba cerrada con 12 testers durante 14 días.
- **Sincronizar entre dispositivos:** agregar un backend propio (por ejemplo Spring Boot + PostgreSQL)
  y que la app guarde ahí en lugar de en el navegador.