# Optional patch — 5-star rating and computed discount badge

The theme's card is kept as-is and restyled by `_gt-product-card.scss`. That gets the Gentoo look
while keeping every feature (wishlist, donations, pre-orders, bookings, out-of-stock notify,
translations, money formatting).

Two things in the design cannot be reached with CSS alone, because the markup isn't there. Both are
**optional** — the card is already correct without them.

Apply these to `src/assets/js/partials/product-card.js`.

---

## 1. Five stars + review count

The theme renders one star icon and the score. The design shows five stars, the score, and the
review count in parentheses.

**Add this method** to the `ProductCard` class (anywhere among the other methods):

```js
  getStars(rating) {
    let out = '';
    for (let i = 1; i <= 5; i++) {
      out += `<i class="sicon-star2 ${rating >= i - 0.5 ? 'gt-star-on' : 'gt-star-off'}"></i>`;
    }
    return `<span class="gt-stars">${out}</span>`;
  }
```

**Then find** this block inside `render()`:

```js
            ${this.product?.rating?.stars ?
              `<div class="s-product-card-rating">
                <i class="sicon-star2 before:text-orange-300"></i>
                <span>${this.product.rating.stars}</span>
              </div>`
               : ``}
```

**and replace it with:**

```js
            ${this.product?.rating?.stars ?
              `<div class="s-product-card-rating">
                ${this.getStars(this.product.rating.stars)}
                <span>${this.product.rating.stars}</span>
                ${this.product?.rating?.count ? `<small class="gt-review-count">(${salla.helpers.number(this.product.rating.count)})</small>` : ''}
              </div>`
               : ``}
```

Add to `_gt-product-card.scss`:

```scss
.gt-stars { display: inline-flex; gap: 1px; }
.gt-star-on::before  { color: #F7941E; }
.gt-star-off::before { color: #e0e0e0; }
.gt-review-count { font: 600 11px/1 Cairo, sans-serif; color: #b3b3b3; }
```

---

## 2. Computed discount percentage badge

The theme shows `promotion_title` — free text the merchant types. The design shows a calculated
`−15%`. This keeps the merchant's text when they set one, and falls back to the computed percentage.

**In `getProductBadge()`**, immediately after the `preorder.label` check and **before** the
`promotion_title` check, insert:

```js
    if (!this.product.promotion_title && this.product.is_on_sale) {
      const regular = Number(this.product.regular_price);
      const sale = Number(this.product.sale_price);
      if (regular > 0 && sale > 0 && regular > sale) {
        const pct = Math.round(((regular - sale) / regular) * 100);
        if (pct > 0) {
          return `<div class="s-product-card-promotion-title">−${pct}%</div>`;
        }
      }
    }
```

---

## Note on theme updates

Both patches edit a file Salla ships, so a future `theme-raed` update may conflict on it. The SCSS
file does not — it is additive. If you want zero merge risk, apply the SCSS only and skip this file.
