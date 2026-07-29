# Gentooshop — where every piece of content comes from

This answers one question per design section: **where does Salla get the data, and where does the
merchant fill it in?**

Verified against this repository's `src/views/pages/product/single.twig` and Salla's documentation.

---

## Native vs custom

| Design section | Native in Salla? | Filled where |
|---|---|---|
| Specifications | ✅ Yes | Product → Custom fields (see note) |
| Reviews and star rating | ✅ Yes | Written by customers |
| SKU / barcode | ✅ Yes | Product → Advanced details |
| Brand, weight, tags, category | ✅ Yes | Product fields |
| Description | ✅ Yes | Product → Description |
| Price, discount, countdown | ✅ Yes | Product → Pricing |
| Remaining / sold quantity | ✅ Yes | Product → Inventory |
| Related products | ✅ Yes | Automatic |
| Installments (Tabby / Tamara) | ✅ Yes | `<salla-installment>`, configured in Salla |
| FAQ | ⚠️ Partial | See below |
| Bundle marketing copy | ❌ No | Our component fields |

---

## Product page

### Specifications

Salla exposes `<salla-metadata>`, already present in the theme:

```twig
{% if product.has_metadata %}
  <salla-metadata data-testid="store-product-metadata"></salla-metadata>
{% endif %}
```

It is fed by **Products → Categories & options → Custom fields**, with
*"Display section to customers on product page"* checked. Sections can be scoped to selected
categories, so power banks and phone cases can carry different spec fields.

**However:** custom fields are **not present in the product Excel export** (verified against a real
export of this store — 42 columns, none of them custom fields). With 460 products that means filling
them by hand, product by product. For that reason this store's specs are being kept **in the product
description** instead, in a fixed structure the theme parses. See `DESCRIPTION-STANDARD.md`.

### Reviews

```twig
{% if store.settings.rating.show_on_product %}
  <salla-comments item-id="{{ product.id }}" type="product"></salla-comments>
{% endif %}
```

Enabled in store settings. Written by customers — `product.rating.stars` and
`product.rating.count` populate on their own. Expect an empty state on a new store; that is correct
behaviour, not a theme fault.

### Fields the design uses — all native

| In the design | Variable | Filled in |
|---|---|---|
| SKU | `product.sku` | Product → SKU |
| Brand + logo | `product.brand.name` / `.logo` | Product → Brand |
| Weight | `product.weight` | Product → Shipping |
| Tags | `product.tags` | Product → Tags |
| Breadcrumbs | `product.category` | Product → Category |
| Price / was-price | `product.sale_price` / `.regular_price` | Product → Pricing |
| Discount percentage | `product.discount_percentage` | Computed |
| Offer countdown | `product.discount_ends` | Product → Sale dates |
| Remaining quantity | `product.quantity` | Product → Inventory |
| Units sold | `product.sold_quantity` | Automatic |
| Images and video | `product.images` | Product → Images |
| Installments | `<salla-installment>` | Configured in Salla |

### FAQ — no native per-product field

Three workable options:

**A.** A custom-fields section named "FAQ" — native and per-product, but renders inside the
specifications block.
**B.** Inside the product description, as a section the theme parses (see
`DESCRIPTION-STANDARD.md`). Per-product, no extra mechanism.
**C.** A theme-level FAQ, identical on every product, edited once.

For this store the questions are largely the same across products (shipping, warranty, returns,
authenticity), so **C** is the recommendation, with **B** available when a product needs its own.

---

## Category page

| In the design | Native? | Notes |
|---|---|---|
| Product grid | ✅ | `<salla-products-list source="categories">` |
| Filters | ✅ | `<salla-filters>`, configured in Salla |
| Sort | ✅ | Native to the list |
| Breadcrumbs | ✅ | `<salla-breadcrumb>` |
| Category title and description | ✅ | Filled when editing the category |
| Free-shipping progress bar | ❌ | Our component; threshold is a theme setting |

---

## Header and footer

Product-independent. Filled in:

- **Menus and categories** → Salla → Menus
- **Logo** → store settings
- **Language and country** → native
- **Social, email, WhatsApp** → store settings
- **Payment badges** → automatic, from the active payment methods
- **Trust badges / tax number** → store settings

---

## Catalogue readiness

From analysis of this store's real export (460 products; a further 650 rows are variant rows):

| Gap | Count | Effect |
|---|---|---|
| No image | 40 | Card and page fall back to a placeholder |
| No price | 18 | Not purchasable |
| No SKU | 30 | The design shows SKU |
| No barcode | 159 | This is how the team searches |
| No promotional title | 458 | Handled — see below |

**Promotional title:** only 2 of 460 are set, so the badge falls back to the **computed discount
percentage**. If a product has its own promotional title, that text wins.

**Good news:** 246 of 460 carry a sale price, median discount 50%. The struck-through price and the
discount badge will show across more than half the catalogue.

Sections render only when their data exists — an empty section never leaves a hole or a bare heading.
