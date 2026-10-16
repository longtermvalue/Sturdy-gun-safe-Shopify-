# Sturdy Safe Shopify Theme (Ritual)

Shopify theme for [sturdysafe.com](https://www.sturdysafe.com) (store `sturdy-safe-3.myshopify.com`).
The repository root is the theme root, so it can be connected with the
[Shopify GitHub integration](https://shopify.dev/docs/storefronts/themes/tools/github)
or pushed with Shopify CLI as is.

- Theme: Ritual 3.5.1 by Shopify (Horizon family)
- Source: admin export `theme_export__www-sturdysafe-com-ritual__16OCT2026-1135am.zip`,
  committed unmodified in the first commit so later changes diff against it.

## Safe configurator

The configurator page (`/pages/safe-configurator`) is made of:

| File | What it is |
|---|---|
| `templates/page.safe-configurator.json` | Page template: the default page section is disabled and the `sturdy-configurator` section renders the app. |
| `sections/sturdy-configurator.liquid` | Mount point, loading state, React 18.3.1 from unpkg, the bundle, and the Ritual compatibility CSS. |
| `assets/sturdy-configurator.min.js` | The storefront bundle. |

The bundle and its Cloudflare Worker live in
[longtermvalue/Sturdy-safe-configurator-](https://github.com/longtermvalue/Sturdy-safe-configurator-)
(`shopify/sturdy-configurator.min.js` and `cloudflare-worker/worker.js`).
That repo is the source of truth for the bundle: copy it here byte for byte
and do not edit `assets/sturdy-configurator.min.js` in this repo. The Worker is
deployed to Cloudflare and is not part of the theme.

### Why the section has a compatibility layer

The bundle was built and tuned on Dawn. Ritual names its font variables
differently and styles bare `h1` to `h6`, `p`, `img` and `svg` elements, so the
section's `<style>` block:

1. maps Dawn's `--font-body-family` / `--font-heading-family` to Ritual's
   `--font-body--family` / `--font-heading--family`, so the configurator uses
   the theme's fonts;
2. pins Dawn's body text metrics on the configurator root;
3. undoes Ritual's uppercase headings, paragraph margin trimming and
   block-level images inside the configurator only;
4. offsets step-change scrolling by Ritual's sticky header height.

Keeping this in the section, not the bundle, means one bundle file works on
both themes.

### Checking a change before publishing

Preview this theme on the real domain so the Worker's checkout endpoint
(CORS-restricted to `https://www.sturdysafe.com`) accepts the request:
`https://www.sturdysafe.com/pages/safe-configurator?preview_theme_id=<theme id>`.
Shared preview links (`*.shopifypreview.com`) and the theme editor run on other
origins, so Checkout Now shows the "please call" fallback there. Everything
else works in those previews.
