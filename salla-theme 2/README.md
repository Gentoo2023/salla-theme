# Gentooshop — Salla Home Page Sections

Drop-in **Twilight** Theme Components for the Gentooshop home page. Every section is registered in
`twilight.json`, so the merchant toggles and reorders all of them from the dashboard — nothing is
hardcoded into page order.

## Design principle: built-ins do the products, we do the look

Per Salla's own best practice, **product listings use Twilight's built-in Theme Features** — we did
not reinvent product grids. The 7 custom components cover only what the built-ins don't: the hero
treatment, the reassurance strips, the video wall, the banner grid, and the section headers.

## Install

1. Merge the `features` and `components` arrays from `twilight.json` into your theme's root
   `twilight.json`. Don't overwrite — merge.
2. Copy `src/views/components/home/*.twig` to the same path in your theme.
3. Confirm `src/views/pages/index.twig` contains `{% component home %}` (ours does).
4. Import the stylesheet from your main entry: `@import "./home.css";`
5. `salla theme serve`, then enable and order the sections in Partners Portal.

## Recommended home page order

This is the design's intended order. It is **not** enforced in code — the merchant can change it.

| # | Section | Type | Path / feature |
|---|---------|------|----------------|
| 1 | Hero banner | Custom | `home.gt-hero` |
| 2 | Trust mini strip | Custom | `home.gt-trust-mini` |
| 3 | Deals header + countdown | Custom | `home.gt-countdown-strip` |
| 4 | Deals product rail | **Built-in** | `component-products-slider` |
| 5 | Promo banner | **Built-in** | `component-fixed-banner` |
| 6 | Product videos | Custom | `home.gt-videos` |
| 7 | Marketing banners grid | Custom | `home.gt-banners` |
| 8 | Recommended products | **Built-in** | `component-fixed-products` |
| 9 | Trust feature cards | Custom | `home.gt-trust` |
| 10 | Recently viewed | Custom | `home.gt-recently` |
| 11 | Selected blog posts | Custom | `home.gt-blog` |

Sections 3 + 4 are a pair: place the countdown strip immediately above the products slider so the
timer reads as that rail's header.

The **category icon row** lives in the header, not the home page.

## Custom component fields

| Component | Merchant fields |
|-----------|-----------------|
| `gt-hero` | image, badge, title, subtitle, cta_text, cta_url |
| `gt-trust-mini` | collection: icon, text (2–6) |
| `gt-countdown-strip` | title, ends_at, link_text, link_url |
| `gt-videos` | title, badge, collection: thumb, url, caption (1–8) |
| `gt-banners` | columns, collection: image, title, url (2–8) |
| `gt-trust` | collection: icon, title, text (2–4) |
| `gt-recently` | title, limit |
| `gt-blog` | title, link_text, link_url, collection: image, title, meta, url (1–6) |

All labels shown to the merchant are Arabic. Icons are Salla's own `sicon-*` font, set per item.

## Product card

`src/assets/js/partials/gt-product-card.js` defines `custom-salla-product-card`, the element name
Salla reserves for a theme's own card. Defining it replaces the default card inside **every**
`<salla-products-list>` in the store — home rails, category, search, related — from this one file.

It reads the product JSON Salla passes on the `product` attribute and renders: image, two-line
clamped name, star rating with score and review count, orange price, strikethrough regular price,
computed discount badge, and a dark add-to-cart pill that calls `salla.cart.addItem`. Products with
options route to the product page instead of adding directly. Sold-out products show an overlay and
a disabled button.

Styles are in `src/assets/styles/gt-product-card.css`.

## Design rules to preserve

- **Brand orange `#F7941E`** — CTAs, badges, icons, active states. Please don't substitute.
- **Ink `#1c1c1c`** headings, `#8a8a8a` secondary text, `#ededed` borders.
- **Font: Cairo**, weights 400–900. Section titles 26px/900 desktop, 20px/900 mobile.
- **Container** max 1300px, 24px side padding, centred.
- **Radii** 18px hero, 14px cards and banners, 12px buttons and chips.
- **Grid gap** 18px desktop, 12px mobile. Sections use a 28px vertical rhythm.
- CSS uses **logical properties only** (`inset-inline`, `margin-inline`), so the Arabic RTL layout
  mirrors correctly with no extra rules.

## Recently viewed — one extra install step

Salla's `<salla-products-list>` has **no browsing-history source**. The documented enum is
`brands | categories | json | latest | offers | related | search | selected | tags`. So this rail is
built the only way it can be: the visitor's own viewed-product IDs are stored in `localStorage` and
passed to `source="selected"`.

That needs the tracker included on the **single product page**. In
`src/views/pages/product/single.twig` add:

```twig
{% include 'components.home.gt-recently-track' %}
```

`gt-recently-track.twig` renders no markup — it just records `product.id`, newest first, capped at
12 entries. Without it the home section stays permanently hidden (it self-hides when there is no
history, so a first-time visitor never sees an empty rail).

## Notes on things we deliberately did not guess

- No `trans()` keys are used in these files. Every piece of visible copy is either a merchant field
  or comes from a built-in component, so nothing depends on a translation key existing.
- `gt-blog` takes merchant-picked posts rather than reading a `blog` global, because that global is
  scoped to the blog pages and is not available inside home components.
- `<salla-count-down>`, `<salla-products-list>` (with the documented `selected` source) are the
  only web components the custom files rely on.
- `ends_at`, `columns` and `limit` are declared as plain `string` fields. The Twilight.json
  reference only documents `boolean`+`switch`, `string` with `image`/`textarea`/`hidden`, and
  `collection`+`collection`; a `number` type and a `date` format are **not** in that reference, so
  we did not use them rather than risk the component failing to register. The expected input is
  stated in each Arabic field label. If your portal does support them, upgrade these three.
