# Tulum Sonrise Tours — sitio web

Sitio estático (HTML, CSS y JS sin frameworks). No necesita compilar nada: lo que está en esta carpeta es exactamente lo que se publica. Funciona igual en **GitHub Pages** y en **Netlify**.

```
index.html          la página (ES / EN)
404.html            página de error
assets/             logo, favicon e imágenes
assets/media/       fotos y videos (comprimidos para web)
netlify.toml        configuración para Netlify (GitHub Pages lo ignora)
.nojekyll           le dice a GitHub Pages que publique tal cual
robots.txt
```

## 1. Antes de publicar: datos del negocio

Abre `index.html`, busca `DATOS DEL NEGOCIO` (casi al final) y cambia:

```js
const WHATSAPP_NUMBER = "529840000000";   // solo dígitos, con 52 de México
const PHONE = "+52 984 000 0000";
const EMAIL = "hola@tulumsonrisetours.com";
const SHOW_PLACEHOLDERS = true;           // ponlo en false cuando todo sea real
```

También revisa en el mismo archivo los textos marcados con `tbc` (horario, idiomas, tiempos de viaje) y el bloque `application/ld+json` del `<head>`.

## 2. Publicar en GitHub Pages

```bash
cd ~/Documents/tulum-sonrise-tours
git init
git add .
git commit -m "Sitio Tulum Sonrise Tours"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/tulum-sonrise-tours.git
git push -u origin main
```

Luego en GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main` / `(root)` → Save**.
En uno o dos minutos queda en `https://TU-USUARIO.github.io/tulum-sonrise-tours/`.

Todas las rutas del sitio son relativas, así que funciona tanto en esa subcarpeta como en un dominio propio.

**Dominio propio en GitHub Pages:** Settings → Pages → Custom domain → escribe `tulumsonrisetours.com` (GitHub crea el archivo `CNAME`). En tu proveedor del dominio agrega los registros que GitHub te indica (4 registros A al apex y un CNAME `www` → `TU-USUARIO.github.io`). Activa **Enforce HTTPS** cuando aparezca.

> Nota: las reglas de GitHub Pages no lo permiten como hosting principal de un negocio comercial. Para empezar está bien, pero si el sitio ya va en serio, pásalo a Netlify (paso 3); no hay que cambiar nada del código.

## 3. Pasar a Netlify (cuando quieras)

Opción A, conectado a GitHub (recomendada):
1. netlify.com → **Add new site → Import an existing project → GitHub** → elige el repositorio.
2. Build command: *(vacío)* · Publish directory: `.` → **Deploy**.
3. Cada `git push` a `main` vuelve a publicar solo.

Opción B, sin Git: arrastra la carpeta completa a **app.netlify.com/drop**.

`netlify.toml` ya trae caché larga para fotos y videos y cabeceras de seguridad. Si conectas un dominio en Netlify y ya lo tenías en GitHub Pages, borra el archivo `CNAME` del repositorio.

## 4. Cuando tengas dominio

Busca `TU-DOMINIO` y cámbialo en:
- `robots.txt` (línea del sitemap, quita el `#`)
- `index.html`: la meta `og:image` y `og:url` deben ser URL completas para que WhatsApp y Facebook muestren la vista previa (ej. `https://tulumsonrisetours.com/assets/media/costa-aerea.jpg`).

## 5. Cambiar fotos o videos

Reemplaza el archivo en `assets/media/` con **el mismo nombre**:
- Fotos: `nombre.jpg` (2560 px de ancho, calidad 88) y `nombre-1280.jpg` (versión para celular).
- Videos: `v-*-hd.mp4` (computadora) y `v-*-sd.mp4` (celular), más `v-*-poster.jpg` (imagen fija mientras carga).

Para preparar un video nuevo en alta calidad (máx. ~20 MB):
```bash
ffmpeg -i original.mp4 -an -c:v libx264 -preset slow -crf 20 -maxrate 6.5M -bufsize 13M -pix_fmt yuv420p -movflags +faststart v-hero-hd.mp4
ffmpeg -i v-hero-hd.mp4 -vf "scale=1280:-2:flags=lanczos" -c:v libx264 -preset slow -crf 22 -maxrate 3M -bufsize 6M -pix_fmt yuv420p -movflags +faststart v-hero-sd.mp4
ffmpeg -i v-hero-hd.mp4 -frames:v 1 -q:v 2 v-hero-poster.jpg
```

Fotos y video: Pexels (licencia libre de uso). Los originales sin comprimir están fuera del repositorio, en `~/Documents/tulum-sonrise-tours-originales/`.
