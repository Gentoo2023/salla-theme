# Cómo instalar — Gentooshop en Salla

Archivos hechos **contra el código real** de `Gentoo2023/salla-theme` (theme-raed v1.358.0).

Todo se **agrega**. No se reemplaza ningún archivo del tema.

---

## 1. Copiar archivos

| Archivo | Destino |
|---|---|
| `src/views/components/home/gt-*.twig` (10 archivos) | misma ruta |
| `src/assets/styles/04-components/_gt-*.scss` (5 archivos) | misma ruta |

## 2. Registrar los estilos

En `src/assets/styles/app.scss`, después de `@import './04-components/product';`:

```scss
@import './04-components/gt-sections';
@import './04-components/gt-product-card';
@import './04-components/gt-header';
@import './04-components/gt-footer';
@import './04-components/gt-shipping-bar';
```

El orden importa: los `gt-` van **después** de los del tema, para que ganen.

## 3. Fusionar twilight.json

En el `twilight.json` del tema (52 KB — **no lo reemplaces**):

- Agrega nuestras 8 entradas al array `components` existente.
- Agrega nuestra entrada al array `settings` (`gt_free_shipping_threshold`).
- Verifica que `features` contenga las entradas que listamos.

## 4. Dos includes manuales

**Página de producto** — `src/views/pages/product/single.twig`, para que "شوهد مؤخرًا" tenga datos:

```twig
{% include 'components.home.gt-recently-track' %}
```

**Página de categoría** — `src/views/pages/product/index.twig`, arriba de la rejilla, para la barra
de envío gratis:

```twig
{% include 'components.home.gt-shipping-bar' %}
```

## 5. Compilar

El tema usa **pnpm** y **webpack**, no npm:

```bash
pnpm install
pnpm run prod
```

Node 22.18+ o 24.11+ (está en `package.json`).

## 6. Previsualizar y publicar

```bash
salla login
salla theme preview
```

Luego en Partners Portal se activan y ordenan las secciones del inicio.

---

## Qué cubre este paquete

| Parte | Qué se hizo |
|---|---|
| **Inicio** | 8 componentes propios + 3 funciones nativas de Salla. Activables y reordenables desde el panel. |
| **Tarjeta de producto** | Re-estilizada. Aparece en inicio, categoría, búsqueda y relacionados. |
| **Header** | Re-estilizado (barra superior, buscador, menú, carrito, menú móvil). |
| **Footer** | Re-estilizado (oscuro, columnas, redes, sellos de pago). |
| **Categoría** | Barra de envío gratis + la rejilla hereda la tarjeta nueva. |
| **Producto** | Pendiente — espera el estándar de descripciones (ver `ESTANDAR-DESCRIPCIONES.md`). |

Móvil y escritorio no son archivos separados: el SCSS es responsivo, con breakpoints en 768px y
1100px.

---

## Decisiones técnicas — leer antes de cambiar algo

**Nada de Salla se reemplaza, todo se re-estiliza.** El header, el footer y la tarjeta de producto
siguen siendo los del tema. Sus componentes (`salla-search`, `salla-menu`, `salla-cart-summary`,
`salla-payments`, `custom-salla-product-card`) mantienen su comportamiento: menú móvil con mmenu,
header sticky, lista de deseos, donaciones, preventas, traducciones, formato de moneda. Reemplazarlos
habría botado todo eso.

**Los campos se leen como `component.<id>`**, no `component.fields.<id>.value`. Confirmado en
`main-links.twig`, `brands.twig` y `enhanced-slider.twig` del repositorio.

**Las secciones usan la estructura del tema:** `<section class="s-block s-block--gt-*"><div class="container">`.

**Los carruseles de productos usan las Theme Features nativas**, no rejillas propias — es la buena
práctica que Salla documenta.

**CSS con propiedades lógicas** (`inset-inline`, `margin-inline`, `border-start-start-radius`). El
RTL árabe se refleja solo.

**Sin claves `trans()` inventadas.** Todo el texto visible viene de un campo del panel o de un
componente nativo.

---

## Diferencias conocidas respecto al diseño

Honestas, para que nadie las descubra tarde:

1. **El buscador está en la barra superior**, no en la barra principal como en el diseño. Moverlo
   requiere editar `header.twig`, lo que toca el markup del tema. Se puede hacer, pero decidimos no
   arriesgarlo en la primera entrega.
2. **La fila de círculos de categorías** no está en el header: en este tema eso es la función
   `component-main-links` de la **página de inicio**. Ahí queda, activable desde el panel.
3. **El footer no tiene newsletter.** theme-raed no lo trae. Si lo quieres, se agrega con un hook.
4. **El footer oscuro se aplica siempre.** Si prefieres controlarlo con el ajuste `footer_is_dark`
   del tema, hay una nota en `_gt-footer.scss` explicando cómo.
