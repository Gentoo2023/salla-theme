# Product description standard — Gentooshop

One rule for all 460 descriptions. Follow it and the theme builds the design's product-page sections
automatically. If a product lacks a section, that section **is not rendered** — no hole, no empty
heading.

This exists because custom fields are absent from Salla's product Excel export, so per-product specs
cannot be bulk-loaded. Descriptions **are** in the export, so they can be edited in bulk and
re-imported.

---

## The rule

**Each section = one `<h2>` + one `<table>`.** Nothing else.

```html
<p>Intro paragraph. Two or three lines.</p>

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

**No attributes, no classes, no `<thead>`.** Only `h2`, `table`, `tr`, `td` — so it survives any
editor and any import round-trip.

### Two cells or one

- **Two `<td>`** → label and value. The normal case.
- **One `<td>`** → a plain line. Useful in the features section when no extra text is needed.

Both work, even mixed inside the same table.

---

## Canonical headings

Chosen to match what this catalogue **already uses most**, to minimise editing.

| Section | Exact heading | Rendered as | Already used by |
|---|---|---|---|
| Features | `المميزات الرئيسية` | Checkmark list | 108 products |
| Specifications | `مواصفات المنتج` | Two-column table | 277 |
| FAQ | `الأسئلة الشائعة` | Accordion | 0 (new) |
| Warranty | `ضمان الثقة` | Shield block | 98 |
| Box contents | `محتويات العلبة` | Bulleted list | 30 |
| What the offer includes | `يشمل هذا العرض` | Highlighted orange block | 16 |

### Any other heading

An `<h2>` whose text is not in the table above renders as a **generic two-column table titled with
that heading**. This is how special products get extra sections with no code change:

```html
<h2>التوافق مع الأجهزة</h2>
<table>
  <tr><td>آيفون</td><td>15 / 16 / 17 وأحدث</td></tr>
</table>
```

---

## Tolerance — so migration can be gradual

The parser also accepts, as fallbacks:

- **Diacritics:** `مواصفات المُنتج` counts as `مواصفات المنتج`.
- **Trailing colon:** `المميزات:` counts as `المميزات`.
- **Synonyms:** `الخصائص` and `المواصفات` count as specifications; `المميزات` and
  `مميزات المنتج` count as features.
- **The current `<ul><li>label: value</li></ul>` format** keeps working.

So descriptions can be converted in batches without breaking the ones not yet touched.

---

## One case that needs rewriting

**27 Goui phone-case products** use one `<h2>` per feature — `حماية موثوقة`,
`توافق مثالي مع أجهزة آبل`, `خامة عملية وعصرية`, `دعم الاستخدام المتعدد`, and so on. Under the rule
above those become seven separate one-row sections, which looks wrong. They should collapse into one
section, with each old heading becoming the first cell and its paragraph the second:

```html
<h2>المميزات الرئيسية</h2>
<table>
  <tr><td>حماية موثوقة</td><td>…the paragraph that followed the h2…</td></tr>
  <tr><td>توافق مثالي مع أجهزة آبل</td><td>…</td></tr>
</table>
```

Mechanical — a find-and-replace pass in the export.

---

## One thing to remove

`منتجات مقترحة` (18 products): hand-written "suggested products" inside the description. Salla
already renders related products at the end of the page, so this duplicates it and goes stale when a
product is retired.

---

## Parser rules, one line each

1. One `<h2>` per section, one `<table>` immediately after it.
2. Only `h2`, `table`, `tr`, `td`. No attributes.
3. Two `<td>` for label and value; one for a plain line.
4. A section with no data is not rendered.
5. A heading outside the canonical list renders as a generic table titled with that heading.
6. Paragraphs before the first `<h2>` are the intro; paragraphs after the last table are the closing
   note.

---

## Spec-table length is variable

Measured across this catalogue: **1 to 26 spec rows per product, median 6.** The table must be
variable-length — there is no fixed schema.
