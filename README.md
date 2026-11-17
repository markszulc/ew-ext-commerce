# Commerce Catalog – Experience Workspace library plugin

A side-panel (library) plugin for [Experience Workspace](https://www.aem.live/docs/ew/developing/apps-plugins) that lets authors search, browse and view the Adobe Commerce product catalog and insert a product SKU into the document.

Built with vanilla JS and [Spectrum Web Components](https://opensource.adobe.com/spectrum-web-components/) (Spectrum 2 theme, light/dark follows the OS).

## Features

- **Search** by name or SKU (debounced, Catalog Service `productSearch`)
- **Browse** by category (from the catalog's category facet) and sort by relevance, name or price
- **Thumbnails** toggle (remembered per browser)
- **Product detail** – image gallery, price, stock, description, attributes, storefront link and variant SKUs for configurable products
- **Insert** into the page using the selected **Insert as** format (SKU text, link, block…), or copy the SKU to the clipboard
- **Configurable insert formats** – block/HTML templates from a `commerce-templates` tab in the site config (see below)
- **Multi-select** – tick several products (selection is kept across searches) and insert them together; block templates produce one block with a row per product
- **Catalog connection** – read automatically from the site's commerce `config.json` (`public.default.commerce-endpoint` + `headers.all`/`headers.cs`, including path-specific overrides like `/fr/`), overridable in the settings panel, with the public Adobe Commerce demo catalog as a fallback

## Files

```
tools/commerce-catalog/
  commerce-catalog.html   # plugin entry page
  commerce-catalog.js     # plugin logic
  commerce-catalog.css    # layout styles (Spectrum tokens)
  vendor/spectrum.js      # vendored Spectrum Web Components bundle (generated)
  src/spectrum.js         # bundle entry – list of Spectrum components used
scripts/serve.mjs         # zero-dependency local server on :3000
```

## Install into your site

1. Copy `tools/commerce-catalog/` into your Edge Delivery Services code repo (including `vendor/`).
2. In your site config (`https://da.live/config#/{ORG}/{SITE}/`), add a row to the `library` tab:

   | title | path | ref |
   | --- | --- | --- |
   | Commerce Catalog | `https://main--{SITE}--{ORG}.aem.live/tools/commerce-catalog/commerce-catalog.html` | |
   | Commerce Catalog (local) | `http://localhost:3000/tools/commerce-catalog/commerce-catalog.html` | local |

3. Edit a document, open **Library** and pick **Commerce Catalog**.

## Insert formats (templates)

Add a `commerce-templates` tab to the same site config (`https://da.live/config#/{ORG}/{SITE}/`; an org-level config is used as a fallback) with two columns:

| name | template |
| --- | --- |
| SKU | `{{sku}}` |
| Product link | `<p><a href="{{url}}">{{name}}</a></p>` |
| Product teaser | `<div class="product-teaser"><div><div>sku</div><div>{{sku}}</div></div></div>` |
| Commerce Card block | `<div class="cards-commerce"><div><div>{{sku}}</div></div></div>` |

- Placeholders: `{{sku}}`, `{{name}}`, `{{price}}`, `{{url}}`, `{{urlKey}}`, `{{image}}` (values are HTML-escaped in HTML templates).
- Plain-text templates are typed at the cursor (`actions.sendText`).
- HTML templates are inserted with `actions.sendHTML`. AEM block markup (`<div class="block-name variant"><div><div>cell</div>…`) is converted to a block table, the same way the Experience Workspace block library does.
- When several products are selected, a block template's rows are repeated once per product inside a single block; other HTML templates are concatenated and text templates become one paragraph per product.
- Without the tab, the built-in defaults above are used. Authors can also override formats for themselves in the panel's settings (stored in their browser; **Reset to site config** removes the override).

## Local development

```sh
npm install
npm run build   # only needed after changing src/spectrum.js
npm start       # serves this folder on http://localhost:3000 (or use `aem up` inside your site repo)
```

Open `http://da.live/edit?ref=local#/{ORG}/{SITE}/index` and choose the local library entry.

Opening the plugin page directly (outside Experience Workspace) also works: "Insert" then copies the SKU to the clipboard.
