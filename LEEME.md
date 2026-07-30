# Qué subir y dónde — Gentooshop

Esta carpeta refleja **exactamente** la estructura de tu repositorio. Sube cada archivo a la misma
ruta, sobrescribiendo el que ya está.

## Archivos que se sobrescriben (4)

| Archivo | Qué le cambié |
|---|---|
| `twilight.json` | Le agregué nuestros 8 componentes y el ajuste de envío gratis. Todo lo que ya tenía el tema sigue intacto. |
| `src/assets/styles/app.scss` | 5 líneas de import para nuestros estilos. |
| `src/views/pages/product/single.twig` | 2 líneas: el tracker de "visto recientemente" y las secciones de la descripción. |
| `src/views/pages/product/index.twig` | 1 línea: la barra de envío gratis. |

## Archivos nuevos (17)

- `src/views/components/home/gt-*.twig` — 10 componentes de la página de inicio
- `src/views/components/product/gt-sections.twig` — **nuevo**: convierte la descripción del
  producto en las secciones del diseño (specs, características, FAQ, garantía, contenido del bundle)
- `src/assets/styles/04-components/_gt-*.scss` — 6 hojas de estilo

## Después de subir

Borra la carpeta `salla-theme 2/` del repositorio. Ya no sirve: sus archivos están ahora en las
rutas correctas, y su `twilight.json` usaba un esquema equivocado.

## Por qué había un problema

Salla importa el tema leyendo el `twilight.json` **de la raíz** del repositorio. Nuestro paquete
estaba en una subcarpeta, así que Salla lo habría ignorado por completo — habrías importado el tema
Raed sin ninguno de nuestros cambios.

Y el `twilight.json` que escribí antes usaba otro esquema del que usa tu tema de verdad:

| Nuestro (mal) | El real |
|---|---|
| `"name": "gt-hero"` | `"key": "<uuid>"` |
| `"title": "بانر رئيسي"` | `"title": {"en": "...", "ar": "..."}` |
| `"theme_name"`, `"version"`, `"repo_url"` | `"name"`, `"repository"`, `"author_email"` |

Los componentes no se habrían registrado. Ahora están en el esquema correcto, verificado contra el
archivo real de tu repositorio.

Un detalle extra: confirmé que tu tema **sí** acepta campos de tipo `number` con formato
`integer`, así que "número de columnas" y "cantidad de productos" ahora son campos numéricos de
verdad en vez de texto.
