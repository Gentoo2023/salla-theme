# Cómo instalar — Gentooshop en Salla

Estos archivos están hechos **contra el código real** de tu repositorio
`Gentoo2023/salla-theme` (theme-raed v1.358.0), no contra la documentación general.

## Qué hay que hacer

Todo se **agrega**. No se sobrescribe ningún archivo del tema, con una sola excepción opcional
(`PATCH-product-card.md`, que puedes ignorar).

### 1. Copiar archivos nuevos

| Archivo | Destino en el repo |
|---|---|
| `src/views/components/home/gt-*.twig` (9 archivos) | misma ruta |
| `src/assets/styles/04-components/_gt-sections.scss` | misma ruta |
| `src/assets/styles/04-components/_gt-product-card.scss` | misma ruta |

### 2. Registrar los estilos

En `src/assets/styles/app.scss`, después de la línea
`@import './04-components/product';`, agregar:

```scss
@import './04-components/gt-sections';
@import './04-components/gt-product-card';
```

### 3. Registrar los componentes

En `twilight.json`, dentro del array `components` que ya existe, agregar las 8 entradas del
`twilight.json` de este paquete. **Solo agregar** — el archivo del tema tiene 52 KB de componentes
que deben quedarse.

En el array `features`, verificar que existan las entradas listadas en nuestro archivo.

### 4. Activar "shoué mucho recientemente"

En `src/views/pages/product/single.twig` agregar una línea:

```twig
{% include 'components.home.gt-recently-track' %}
```

Sin esto la sección de "shoué recientemente" queda oculta a propósito (no muestra una fila vacía).

### 5. Compilar

El tema usa **pnpm** y **webpack**, no npm:

```bash
pnpm install
pnpm run prod
```

Requiere Node 22.18+ o 24.11+ (está en `package.json`).

### 6. Previsualizar

```bash
salla login
salla theme preview
```

### 7. Activar las secciones

En Partners Portal, en el preview del tema, aparecen las 8 secciones nuevas. Se activan y se ordenan
arrastrando. Los campos (imágenes, títulos, links) los llena quien administre la tienda.

---

## Decisiones técnicas importantes

Vale la pena que el desarrollador de Salla lea esto — evita que deshaga cosas a propósito.

**La tarjeta de producto NO se reemplaza, se re-estiliza.**
`src/assets/js/partials/product-card.js` del tema ya maneja lista de deseos, donaciones, preventas,
reservas, avisos de agotado, traducciones y formato de moneda. Reemplazarla habría botado todo eso.
`_gt-product-card.scss` re-estiliza su markup existente. Se conserva el 100% de la funcionalidad.

**Los campos se leen como `component.<id>`.**
No `component.fields.<id>.value`. Confirmado leyendo `main-links.twig`, `brands.twig` y
`enhanced-slider.twig` del repositorio.

**Las secciones siguen la estructura del tema.**
`<section class="s-block s-block--gt-*"><div class="container">`, igual que los componentes propios
de Salla, para que hereden el ritmo vertical y los anchos del tema.

**Los carruseles de productos usan las Theme Features de Salla**, no rejillas propias — es la buena
práctica que Salla documenta. Nuestro `gt-countdown-strip` va justo encima de un
`component-products-slider` y le hace de encabezado.

**CSS con propiedades lógicas.** `inset-inline`, `margin-inline`, `border-start-start-radius`. El RTL
árabe se refleja solo, sin reglas extra.

---

## Qué cubre y qué falta

**Cubierto:** las 11 secciones de la página de inicio, y la apariencia de la tarjeta de producto en
todo el sitio (inicio, categorías, búsqueda, relacionados).

**Falta:** header, footer, la página de producto y la de categoría. Se pueden convertir igual.
