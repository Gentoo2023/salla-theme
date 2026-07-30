# Install guide — Gentooshop theme package

Built **against the real source** of `Gentoo2023/salla-theme` (theme-raed v1.358.0), not against
generic documentation.

Everything here is **additive**. No theme file is replaced.

---

## 1. Copy the new files

| Files | Destination |
|---|---|
| `src/views/components/home/gt-*.twig` (10 files) | same path |
| `src/views/components/product/gt-sections.twig` | same path |
| `src/assets/styles/04-components/_gt-*.scss` (7 files) | same path |

## 2. Register the styles

In `src/assets/styles/app.scss`, **after** `@import './04-components/product';`:

```scss
@import './04-components/gt-sections';
@import './04-components/gt-product-card';
@import './04-components/gt-header';
@import './04-components/gt-footer';
@import './04-components/gt-shipping-bar';
@import './04-components/gt-product-sections';
@import './04-components/gt-product-page';
```

Order matters — the `gt-` partials must come after the theme's own so they win.

## 3. Merge twilight.json

In the theme's `twilight.json` (52 KB — **do not replace it**):

- Add our 8 entries to the existing `components` array.
- Add our entry to the `settings` array (`gt_free_shipping_threshold`).
- Make sure `features` contains the entries listed in our file.

## 4. Three manual includes

**Product page** — `src/views/pages/product/single.twig`, so the "Recently viewed" rail has data:

```twig
{% include 'components.home.gt-recently-track' %}
```

**Category page** — `src/views/pages/product/index.twig`, above the product grid, for the
free-shipping bar:

```twig
{% include 'components.home.gt-shipping-bar' %}
```

**Product page** — `src/views/pages/product/single.twig`, immediately after the
`.product__description` block closes, for the parsed description sections:

```twig
{% include 'components.product.gt-sections' %}
```

## 5. Build

The theme uses **pnpm** and **webpack**, not npm:

```bash
pnpm install
pnpm run prod
```

Requires Node 22.18+ or 24.11+ (per `package.json`).

## 6. Preview and publish

```bash
salla login
salla theme preview
```

Then enable and order the home sections in Partners Portal.

---

## What this package covers

| Area | What was done |
|---|---|
| **Home** | 8 custom Theme Components + 3 native Salla features. All toggleable and reorderable from the dashboard. |
| **Product card** | Restyled. Applies on home rails, category, search and related products. |
| **Header** | Restyled (top strip, search, main menu, cart, mobile drawer). |
| **Footer** | Restyled (dark, columns, social, payment badges). |
| **Category** | Free-shipping progress bar + the grid inherits the new card. |
| **Product page** | Description parsed into styled sections: features, specs, FAQ, warranty, offer contents. See below. |

Mobile and desktop are not separate files: the SCSS is responsive, with breakpoints at 768px and
1100px.

**Product page, mobile vs desktop.** `_gt-product-page.scss` restyles the theme's own markup, so
the gallery keeps its 3D and video slides and zoom, and the buy button keeps wishlist, pre-orders,
bookings, donations, out-of-stock notify and money formatting. Below 768px the thumbnails become a
horizontal swipe strip and `.sticky-product-bar` pins to the bottom of the viewport with quantity
and add-to-cart on one row; above it the thumbnails become a vertical rail beside the main image and
the buy box becomes a card. When the merchant turns on the theme's own `sticky_add_to_cart`, the
theme floats the button itself — we keep the skin and drop our pinning so the two do not fight.

---

## The product page reads the description

Salla has no per-product spec fields a theme can bulk-load — custom fields are **absent from the
product Excel export** (verified against a real 460-product export of this store: 42 columns, none
of them custom fields). Descriptions **are** in the export, so this store keeps its specs, features,
FAQ and warranty inside the description, in the fixed structure in `DESCRIPTION-STANDARD.md`: one
`<h2>` followed by one `<table>`.

`components/product/gt-sections.twig` reads the description Salla already rendered, splits it on
those headings, and rebuilds each part with the Gentoo design. Six canonical headings render
differently (features → tick list, specs → sidebar table, FAQ → accordion, warranty → green block,
offer contents → numbered orange block); **any other heading renders as a generic titled list**, so
merchants can add sections with no code change.

It is deliberately forgiving, because the catalogue is not uniform:

- `<tbody>`, `style=` on the table and `data-row=` on cells are all ignored.
- A **single** `<td>` holding `label: value` is split into two columns — but only when the label is
  ≤42 characters, so a long sentence containing a colon stays one full-width line.
- Diacritics are stripped (`مواصفات المُنتج` = `مواصفات المنتج`), trailing colons dropped
  (`المميزات:` = `المميزات`), and synonyms accepted (`الخصائص`, `خصائص المنتج` → specs).
- **Repeated sections of the same kind are all kept.** Several products carry both
  `مواصفات المنتج` and `خصائص المنتج`; both render, each with its own heading. An earlier version
  took only the first and silently dropped 14 rows of electrical specs.
- The old `<ul><li>label: value</li></ul>` format still parses, so descriptions can be migrated in
  batches.
- **If nothing recognisable is found the script does nothing** and the theme's own description stays
  visible. There is no state where the customer sees an empty page.

Verified against five real cases from this catalogue: the OG2300 bundle, the Titan 100 W charger
(two spec tables), an iPhone 16 case (two-cell FAQ + diacritics), the legacy list format, and a
description with no structure at all.

---

## Technical decisions — please read before changing anything

**Nothing from Salla is replaced; everything is restyled.** The header, footer and product card are
still the theme's own. Their components (`salla-search`, `salla-menu`, `salla-cart-summary`,
`salla-payments`, `custom-salla-product-card`) keep all behaviour: the mmenu mobile drawer, the
sticky header, wishlist, donations, pre-orders, bookings, out-of-stock notify, translations and money
formatting. Replacing them would have thrown all of that away.

**Component fields are read as `component.<id>`**, not `component.fields.<id>.value`. Verified
against `main-links.twig`, `brands.twig` and `enhanced-slider.twig` in this repository.

**Sections follow the theme's structure:**
`<section class="s-block s-block--gt-*"><div class="container">`.

**Product rails use the native Theme Features**, not hand-rolled grids — Salla's own documented best
practice. `gt-countdown-strip` is designed to sit directly above a `component-products-slider` and
act as its header.

**CSS uses logical properties only** (`inset-inline`, `margin-inline`, `border-start-start-radius`),
so the Arabic RTL layout mirrors with no extra rules.

**No invented `trans()` keys.** Every piece of visible copy comes either from a dashboard field or
from a native component.

**Field types were kept conservative.** `ends_at`, `columns` and `limit` are declared as plain
`string` settings. The Twilight.json reference only documents `boolean`+`switch`, `string` with
`image`/`textarea`/`hidden`, and `collection`+`collection`; a `number` type and a `date` format are
not in that reference, so we did not risk the components failing to register. Expected input is
stated in each Arabic field label. If the Partners Portal does support them, feel free to upgrade.

---

## Known differences from the design

Stated up front rather than discovered later:

1. **The search bar sits in the top strip**, not in the main bar as the design shows. Moving it means
   editing `header.twig` markup, which we chose not to risk in the first delivery. Happy to do it if
   you prefer.
2. **The category icon circles are not in the header.** In this theme that is the
   `component-main-links` feature on the **home page**, where it works and stays merchant-editable.
3. **The footer has no newsletter block.** theme-raed does not ship one; it can be added via a hook.
4. **The dark footer applies unconditionally.** If you would rather drive it from the theme's
   `footer_is_dark` setting, there is a note at the top of `_gt-footer.scss`.
5. **Recently viewed needs the tracker include** from step 4, because
   `<salla-products-list>` has no browsing-history source — the documented enum is
   `brands | categories | json | latest | offers | related | search | selected | tags`. We store the
   visitor's own product ids in `localStorage` and feed `source="selected"`. The section self-hides
   when there is no history, so a first-time visitor never sees an empty rail.

---

## Optional

`PATCH-product-card.md` describes two small edits to the theme's `product-card.js` — a five-star
rating with review count, and a computed discount percentage badge. Both are optional; the card is
correct without them. They are the only changes in this package that touch a Salla-shipped file, so
skip them if you want zero merge risk on theme updates.
