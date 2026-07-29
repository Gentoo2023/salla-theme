# Estándar de descripción de producto — Gentooshop

Regla única para las 460 descripciones. Si la sigues, el tema arma las secciones del diseño solo.
Si un producto no tiene una sección, esa sección **no aparece** — no queda hueco ni título vacío.

---

## La regla

**Cada sección = un `<h2>` + una `<table>`.** Nada más.

```html
<p>Párrafo de introducción. Dos o tres líneas.</p>

<h2>المميزات الرئيسية</h2>
<table>
  <tr><td>شاشة رقمية</td><td>تعرض نسبة الشحن المتبقية بدقة</td></tr>
  <tr><td>شحن سريع 22.5 واط</td><td>يدعم الباور ديلفري وكوالكوم 3.0</td></tr>
</table>

<h2>مواصفات المنتج</h2>
<table>
  <tr><td>السعة</td><td>20000 ملي أمبير</td></tr>
  <tr><td>القدرة</td><td>22.5 واط</td></tr>
</table>

<h2>الأسئلة الشائعة</h2>
<table>
  <tr><td>هل يمكن حمله في الطيران؟</td><td>نعم، السعة مسموحة في الحقيبة اليدوية.</td></tr>
</table>
```

**Sin atributos, sin clases, sin `<thead>`.** Solo `h2`, `table`, `tr`, `td`. Así sobrevive
cualquier editor y cualquier importación.

### Dos columnas o una

- **Dos `<td>`** → etiqueta y valor. Es lo normal.
- **Un `<td>`** → línea simple. Útil en المميزات cuando no quieres texto extra.

Las dos formas funcionan, incluso mezcladas en la misma tabla.

---

## Títulos canónicos

Elegí los que **ya usas más**, para que edites lo menos posible.

| Sección | Título exacto | Cómo se ve en el diseño | Ya lo usan |
|---|---|---|---|
| Características | `المميزات الرئيسية` | Lista con ✓ naranja | 108 |
| Especificaciones | `مواصفات المنتج` | Tabla de dos columnas | 277 |
| Preguntas frecuentes | `الأسئلة الشائعة` | Acordeón desplegable | 0 (nuevo) |
| Garantía | `ضمان الثقة` | Bloque con ícono de escudo | 98 |
| Contenido de la caja | `محتويات العلبة` | Lista con viñetas | 30 |
| Qué incluye la oferta | `يشمل هذا العرض` | Bloque destacado naranja | 16 |

### Cualquier otro título

Si pones un `<h2>` con un título que no está en la tabla, el tema lo muestra como **tabla genérica
de dos columnas con ese título**. Así resuelves los productos especiales sin pedirme nada:

```html
<h2>التوافق مع الأجهزة</h2>
<table>
  <tr><td>آيفون</td><td>15 / 16 / 17 وأحدث</td></tr>
</table>
```

---

## Tolerancia (para que no tengas que migrar todo de golpe)

El parser también acepta, como respaldo:

- Diacríticos: `مواصفات المُنتج` cuenta igual que `مواصفات المنتج`.
- Dos puntos al final: `المميزات:` cuenta igual que `المميزات`.
- Sinónimos: `الخصائص` y `المواصفات` cuentan como especificaciones. `المميزات` y `مميزات المنتج`
  cuentan como características.
- `<ul><li>etiqueta: valor</li></ul>` en vez de tabla — sigue funcionando (es tu formato actual).

O sea: puedes migrar por lotes. Lo viejo no se rompe mientras conviertes.

---

## Lo que hay que arreglar en el catálogo

Aparte del formato, esto sale del análisis de los 460 productos:

| Problema | Cuántos | Por qué importa |
|---|---|---|
| Sin imagen | 40 | La tarjeta y la página salen con placeholder |
| Sin precio | 18 | No se pueden comprar |
| Sin SKU | 30 | El diseño muestra el SKU |
| Sin barcode | 159 | Es como tu equipo busca |
| Sin título promocional | 458 | Ver la nota de abajo |

**Sobre el título promocional:** solo 2 de 460 lo tienen. El diseño lo usa para el badge sobre la
imagen. Mi decisión: **si está vacío, el badge muestra el porcentaje de descuento calculado**
(`−50%`). Así no tienes que llenar 458 campos. Si algún día quieres texto propio en un producto
—«الأكثر مبيعًا»— lo escribes ahí y ese texto gana.

**Lo bueno:** 246 de 460 tienen precio rebajado, con descuentos de mediana 50%. El precio tachado y
el badge se van a ver en más de la mitad del catálogo.

---

## Caso que sí necesita reescritura

Los **27 kfrat de Goui** usan un `<h2>` por cada característica: `حماية موثوقة`,
`توافق مثالي مع أجهزة آبل`, `خامة عملية وعصرية`, `دعم الاستخدام المتعدد`, etc.

Con la regla nueva eso saldría como 7 secciones separadas, cada una con una tabla de una fila. Se ve
mal. Hay que convertirlos a una sola sección:

```html
<h2>المميزات الرئيسية</h2>
<table>
  <tr><td>حماية موثوقة</td><td>...el texto que estaba debajo del h2...</td></tr>
  <tr><td>توافق مثالي مع أجهزة آبل</td><td>...</td></tr>
  <tr><td>خامة عملية وعصرية</td><td>...</td></tr>
</table>
```

El título de cada `h2` se vuelve la primera columna. El párrafo que venía debajo, la segunda. Es
mecánico — se puede hacer con buscar y reemplazar en el Excel.

---

## Lo que hay que quitar

**`منتجات مقترحة`** (18 productos): productos sugeridos escritos a mano dentro de la descripción.
Salla ya muestra relacionados automáticamente al final de la página, así que esto duplica y además
envejece — si retiras un producto, el enlace queda roto. Recomiendo borrarlo.

---

## Ejemplo completo, con un producto real tuyo

Tomando `قوي - نانو 20 برو` (SKU G-TPB20PRO-T), así queda su descripción con el estándar:

```html
<p>بطارية متنقلة قوي نانو 20 برو بسعة 20000 ملي أمبير وقدرة 22.5 واط، بتصميم شفاف يلائم الحقيبة وشاشة رقمية تتيح لك معرفة نسبة الشحن في أي وقت.</p>

<h2>المميزات الرئيسية</h2>
<table>
  <tr><td>شاشة رقمية</td><td>تعرض نسبة الشحن المتبقية في أي وقت</td></tr>
  <tr><td>شحن سريع</td><td>يدعم الباور ديلفري PD وكوالكوم 3.0</td></tr>
  <tr><td>منفذان</td><td>شحن جهازين في الوقت نفسه عبر USB-C و USB-A</td></tr>
  <tr><td>سعة كبيرة</td><td>تكفي لإعادة شحن أجهزتك أكثر من مرة</td></tr>
  <tr><td>تصميم عملي</td><td>لا يشغل حيزًا كبيرًا في الحقيبة</td></tr>
</table>

<h2>مواصفات المنتج</h2>
<table>
  <tr><td>العلامة التجارية</td><td>قوي</td></tr>
  <tr><td>النوع</td><td>بطارية متنقلة</td></tr>
  <tr><td>السعة</td><td>20000 ملي أمبير</td></tr>
  <tr><td>القدرة</td><td>22.5 واط</td></tr>
  <tr><td>الإدخال</td><td>Type-C PD: DC 5V/3A, 9V/2A, 12V/1.5A</td></tr>
  <tr><td>الإخراج Type-A QC</td><td>DC 5V/3A, 9V/2A, 10V/2.25A, 12V/1.5A (22.5W Max)</td></tr>
  <tr><td>الإخراج Type-C PD</td><td>DC 5V/3A, 9V/2.22A, 12V/1.67A (20W Max)</td></tr>
  <tr><td>إجمالي الإخراج</td><td>DC 5V/3A Max</td></tr>
</table>

<h2>ضمان الثقة</h2>
<table>
  <tr><td>مدة الضمان</td><td>سنة واحدة من الوكيل الرسمي</td></tr>
  <tr><td>الإرجاع</td><td>مجاني خلال 14 يومًا</td></tr>
</table>

<h2>الأسئلة الشائعة</h2>
<table>
  <tr><td>هل يمكن حمله في الطيران؟</td><td>نعم، السعة مسموح بها في الحقيبة اليدوية.</td></tr>
  <tr><td>هل يشحن الأجهزة اللوحية؟</td><td>نعم، متوافق مع الهواتف والأجهزة اللوحية وأجهزة البلوتوث.</td></tr>
</table>

<p>مناسبة للسفر والرحلات الطويلة.</p>
```

Nota lo que gané en los specs: separé `الإخراج` en dos etiquetas distintas
(`الإخراج Type-A QC` y `الإخراج Type-C PD`). En tu versión actual la palabra `الإخراج` se repetía
dos veces y la tabla mostraba dos filas con la misma etiqueta.

---

## Reglas de oro, en una línea cada una

1. Un `<h2>` por sección, una `<table>` justo debajo.
2. Solo `h2`, `table`, `tr`, `td`. Sin atributos.
3. Dos `<td>` para etiqueta y valor; uno para línea simple.
4. La sección que no exista, no se muestra.
5. Un título fuera de la lista canónica sale como tabla genérica con ese título.
6. El párrafo antes del primer `<h2>` es la introducción. Los de después del último, el cierre.
