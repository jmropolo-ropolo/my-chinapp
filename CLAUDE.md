# Mi chino HSK 1 — guía para trabajar en este repo

App personal para estudiar chino (HSK 1). Es **un único `index.html`** servido por
GitHub Pages en https://jmropolo-ropolo.github.io/my-chinapp/ — sin build, sin
dependencias, sin `package.json`. **El repo es público**: todo lo que se sube lo ve
cualquiera.

## No leas `index.html` entero

Pesa 1,7 MB y tiene 3.140 líneas, pero casi todo el peso son **cinco líneas de datos**
(imágenes en base64 y diccionarios): las **1249, 1255, 1256, 1414 y 1416**, de entre
30 KB y 485 KB cada una. Leerlas llena el contexto y no aportan nada.

Buscá con `grep -n` y leé por rangos con `sed -n 'A,Bp' index.html | cut -c1-300`
(el `cut` protege de las líneas gigantes). El JavaScript está dividido con marcadores
`/* ===== nombre ===== */`:

| Líneas | Sección |
|---|---|
| 1–766 | `<head>` y todo el CSS |
| 768–1247 | HTML de las pantallas |
| 1248–1253 | librería Hanzi Writer incrustada (la 1249 es gigante) |
| 1254–1422 | datos del curso (las 1255, 1256, 1414 y 1416 son gigantes) |
| 1423–1563 | utilidades, fechas/puntos/racha, voz y ajustes, reproductor |
| 1564–1773 | navegación, inicio, lo básico, vocabulario, tarjetas, test |
| 1774–2187 | hablar, escribir, micrófono, conversaciones guiadas, diálogos, tonos, frases |
| 2188–2316 | **cuentas y sincronización**, perfiles |
| 2317–2929 | iconos, seguir el libro, diseño, fonética, recorrido de lecciones, gramática |
| 2930–3140 | cuaderno, micrófono fijo, logros y medallas, inicio de la app |

Los números se mueven con cada cambio: confirmalos con
`grep -n '^/\* =====' index.html`.

## Sincronización: qué es público y qué no

- La clave de Supabase del HTML (`CLOUD.key`, `sb_publishable_…`) es la **publishable**:
  está pensada para ir en el cliente. **Nunca pegues aquí una clave `sb_secret_…` ni una
  `service_role`**: el repo es público y quedaría expuesta para siempre en el historial.
- El cliente sólo llama a dos funciones: `cargar_progreso` y `guardar_progreso`. La tabla
  no se lee directamente (con la clave pública responde 401). Si hace falta un dato
  nuevo, se añade a una función en Supabase; **no** abras la tabla al cliente.
- La cuenta es el hash SHA-256 del teléfono + un PIN de 4 cifras. Es poca protección a
  propósito (sólo guarda progreso de estudio): no guardes ahí nada más sensible.
- El progreso vive en `localStorage` con las claves de `SYNC_KEYS`. Si añadís una clave
  nueva, añadila a `SYNC_KEYS` **y** a `mergeData()`, o no se sincroniza o se pisa.

## Probar y publicar

- No hay tests. Se prueba abriendo `index.html` en el navegador (el micrófono y la voz
  necesitan HTTPS o `localhost`: `python3 -m http.server`).
- **Publicar es hacer push a `main`**: GitHub Pages lo sirve tal cual en un par de
  minutos. No hay paso intermedio, así que probá antes.
- El último commit lleva la versión al final del mensaje, `(v2026-10-23.2)`: seguí ese
  formato (fecha + número del día).

## Licencias de terceros

Ilustraciones de OpenMoji (CC BY-SA 4.0) y trazos de hanzi-writer-data (Arphic Public
License). La atribución está en el `README.md`: mantenela si añadís material.
