# Gentooshop — de dónde sale cada dato

Este documento responde una pregunta concreta: **para cada sección del diseño, ¿de dónde saca Salla
la información, y dónde la llena el comerciante?**

Verificado contra el código real del tema (`theme-raed` v1.358.0) y la documentación de Salla, no
supuesto.

---

## Resumen: qué es nativo y qué no

| Sección del diseño | ¿Nativo en Salla? | Dónde se llena |
|---|---|---|
| Especificaciones / المواصفات | ✅ Sí | Producto → Especificaciones |
| Reseñas y estrellas | ✅ Sí | Las escriben los clientes |
| SKU / código de barras | ✅ Sí | Producto → SKU |
| Marca, peso, etiquetas, categoría | ✅ Sí | Campos del producto |
| Descripción | ✅ Sí | Producto → Descripción |
| Precio, descuento, cuenta regresiva | ✅ Sí | Producto → Precios y oferta |
| Cantidad restante / vendidos | ✅ Sí | Producto → Inventario |
| Productos relacionados | ✅ Sí | Automático |
| Preguntas frecuentes (FAQ) | ⚠️ Parcial | Ver más abajo |
| "Por qué esta baqueta" (bundle) | ❌ No | Campos de nuestro componente |

---

## Página de producto

### Especificaciones — ✅ nativo

Salla tiene un sistema de especificaciones por producto. El componente
`<salla-metadata>` las muestra, y **ya está en el tema**:

```twig
{% if product.has_metadata %}
  <salla-metadata data-testid="store-product-metadata"></salla-metadata>
{% endif %}
```

Soporta varias secciones y admite texto, enlaces y archivos.

**Dónde se llena:** en el panel de Salla, editando el producto, en la sección de especificaciones.
Una vez llenas eso, `product.has_metadata` se vuelve verdadero y la sección aparece sola.

**Lo que hacemos nosotros:** darle el estilo del diseño. Los datos son de Salla.

### Reseñas y estrellas — ✅ nativo

```twig
{% if store.settings.rating.show_on_product %}
  <salla-comments item-id="{{ product.id }}" type="product"></salla-comments>
{% endif %}
```

Y las estrellas del encabezado:

```twig
<salla-rating-stars value="{{ product.rating.stars }}" reviews="{{ product.rating.count }}">
```

**Dónde se activa:** Ajustes de la tienda → Tasaciones/Reseñas → mostrar en la página de producto.
No se "llenan" — las escriben los clientes. `product.rating.stars` y `product.rating.count` salen
solos.

### Campos sueltos que el diseño usa — ✅ todos nativos

| En el diseño | Variable | Se llena en |
|---|---|---|
| SKU / código de barras | `product.sku` | Producto → SKU |
| Marca y su logo | `product.brand.name` / `.logo` | Producto → Marca |
| Peso | `product.weight` | Producto → Envío |
| Etiquetas | `product.tags` | Producto → Etiquetas |
| Categoría (migas de pan) | `product.category` | Producto → Categoría |
| Precio y precio anterior | `product.sale_price` / `.regular_price` | Producto → Precios |
| Porcentaje de descuento | `product.discount_percentage` | Se calcula solo |
| Fin de la oferta (cuenta regresiva) | `product.discount_ends` | Producto → Oferta |
| Cantidad restante | `product.quantity` | Producto → Inventario |
| Cuántos se vendieron | `product.sold_quantity` | Automático |
| Imágenes y videos | `product.images` | Producto → Imágenes |
| Cuotas (Tabby/Tamara) | `<salla-installment>` | Se configura en Salla |

### Preguntas frecuentes (FAQ) — ⚠️ no hay campo nativo por producto

Salla **no tiene** un campo de FAQ por producto que un tema pueda leer. Tres opciones reales:

**Opción A — usar las especificaciones (recomendada si quieres FAQ por producto)**
Crear una sección de especificaciones llamada "Preguntas frecuentes" y poner ahí las preguntas.
Sale dentro del bloque de especificaciones, no como acordeón aparte, pero es nativo y por producto.

**Opción B — FAQ a nivel de tema (recomendada para la mayoría de tiendas)**
Un componente nuestro con preguntas configurables desde el panel, iguales en todos los productos.
Sirve para lo que la gente realmente pregunta: envío, garantía, devoluciones, autenticidad.
Se llena una vez y listo.

**Opción C — dentro de la descripción**
El comerciante escribe las preguntas en la descripción del producto. Cero desarrollo, pero el
formato depende de quién escriba.

**Nuestra recomendación:** Opción B. Las FAQ de una tienda de accesorios casi nunca cambian por
producto, y así no hay que llenar lo mismo en cada uno.

### "Por qué esta baqueta" — ❌ no nativo

Es una sección de marketing, no un dato del producto. Los textos se llenan en los campos de nuestro
componente desde el panel del tema.

---

## Página de inicio

Todo se llena en el panel del tema, sección por sección. Ya está entregado — ver `INSTALL.md`.

Las listas de productos (ofertas, recomendados) usan las funciones nativas de Salla, así que los
productos se eligen en el panel, no se escriben a mano.

---

## Página de categoría

| En el diseño | ¿Nativo? | Notas |
|---|---|---|
| Rejilla de productos | ✅ | `<salla-products-list source="categories">` |
| Filtros (marca, precio, etc.) | ✅ | `<salla-filters>` — se configuran en Salla |
| Ordenar por | ✅ | Nativo de la lista |
| Migas de pan | ✅ | `<salla-breadcrumb>` |
| Título y descripción de la categoría | ✅ | Se llenan al editar la categoría |
| Barra de envío gratis | ❌ | Nuestro componente; el monto se configura como campo |

---

## Header y footer

No dependen del producto. Se llenan en:

- **Menús y categorías** → Salla, sección de menús
- **Logo** → Ajustes de la tienda
- **Idioma y país** → nativo de Salla
- **Redes sociales, correo, WhatsApp** → Ajustes de la tienda
- **Métodos de pago del footer** → automático según lo que tengas activo
- **Boletín/newsletter** → nativo de Salla

---

## Lo que hay que hacer en Salla antes de que el diseño se vea completo

Aunque el tema esté instalado, estas secciones salen vacías si nadie llena la información:

1. **Especificaciones en cada producto** — es lo que alimenta la tabla de specs.
2. **Activar reseñas** en los ajustes de la tienda.
3. **SKU en cada producto** — el diseño lo muestra.
4. **Marca y logo** por producto.
5. **Categorías con imagen** — el header las usa.
6. **Menús** armados en Salla — el mega menú los lee.

Sin esto el tema funciona, pero se ve incompleto. Vale la pena revisarlo antes de juzgar el
resultado.
