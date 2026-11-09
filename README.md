# Gaadi Tracker website

Marketing site for Gaadi Tracker, the fleet management and driver hisab app for taxi, auto and truck owners in India.

- **Stack:** [Astro](https://astro.build) (static output), [Tailwind CSS](https://tailwindcss.com) v4, MDX for guides and legal pages.
- **Font:** Google Sans (open source, OFL, from Google Fonts), self-hosted through Fontsource. One family covers headings, body, Hindi (Devanagari) and the ₹ sign.
- **JavaScript sent to visitors:** only the theme toggle, menus, the pay calculator and the contact form. Everything else is plain HTML.
- **Hosting:** GitHub Pages. `npm run deploy` builds the site and publishes it.

## Run it locally

Needs Node 20 or newer.

```bash
npm install
npm run dev        # http://localhost:4321
npm run build      # type-checks, then builds to dist/
npm run preview    # serves dist/ exactly as it will be deployed
```

## Where things live

```
public/                 logo, icons, robots.txt, llms.txt, assetlinks.json
src/data/site.ts        ← contact details, Play package IDs, GA4, Formspree, placeholder switch
src/data/site-url.mjs   ← the live domain (used for canonicals, sitemap, share images)
src/data/faq.ts         FAQ questions and answers (English + Hindi homepage set)
src/i18n/ui.ts          navigation and footer text, English + Hindi
src/i18n/home.ts        homepage copy, English + Hindi
src/i18n/vehicles.ts    taxi / auto / truck page copy, English + Hindi
src/components/         page sections; screens/ holds the mock app screens
src/content/blog/       guides, one .mdx file each
src/pages/              one file per URL
src/pages/og/           builds the 1200×630 share image for every page
src/styles/global.css   colour tokens (light + dark), type, shared classes
```

## Edit content

- **Text on the homepage or vehicle pages:** edit `src/i18n/home.ts` or `src/i18n/vehicles.ts`. English and Hindi sit next to each other, so change both.
- **FAQ:** `src/data/faq.ts`. Keep each answer complete on its own (40–80 words); search engines and AI assistants quote them directly.
- **Contact details, links, analytics:** `src/data/site.ts`.
- **Colours:** the tokens at the top of `src/styles/global.css`. Light and dark are defined separately.

## Add a guide

1. Create `src/content/blog/your-slug.mdx`:

   ```mdx
   ---
   title: The headline people will search for
   metaTitle: Under 60 characters for Google
   description: Under 155 characters, written for the search result.
   summary: One or two sentences shown under the headline and on the guides list.
   published: 2026-11-20
   updated: 2026-11-20
   readMinutes: 5
   ---

   Your article in Markdown. Use ## for sections.
   ```

2. It appears at `/blog/your-slug`, on the guides list, in the sitemap, and gets its own share image. Update `updated:` whenever you change it: the date is shown on the page.
3. Add a link to it from `public/llms.txt`.

Set `draft: true` to keep a guide out of the build.

## Replace the mock screens with real screenshots

The phone screens are drawn in HTML (`src/components/screens/`) with realistic example data. When real screenshots are ready:

1. Put them in `src/assets/screens/` (PNG, portrait, around 1080 px wide).
2. In the page, replace the screen component inside its `<Placeholder>` with Astro’s image component:
   ```astro
   ---
   import { Image } from 'astro:assets';
   import dash from '../assets/screens/owner-dashboard.png';
   ---
   <Image src={dash} alt="Owner dashboard showing today’s revenue across three cabs" width={272} class="rounded-[38px]" />
   ```
   Astro produces AVIF/WebP automatically.
3. When everything is real, set `showPlaceholders: false` in `src/data/site.ts` to remove the dashed outlines.

## Google Play badges

Download the official badges from <https://play.google.com/intl/en_us/badges/> and save them as `public/badges/google-play-en.png` and `public/badges/google-play-hi.png`. The site switches to them automatically; until then a plain text button is shown.

## Deploy

```bash
npm run deploy
```

That type-checks, builds `dist/`, and pushes the built site to the `gh-pages` branch. The publish includes `.nojekyll`, so GitHub Pages serves the `_astro` assets as static files.

In the repo, set **Settings → Pages → Build and deployment → Source** to **Deploy from a branch**, branch **`gh-pages`**, folder **`/ (root)`**.

Pushes to `main` also run `.github/workflows/deploy.yml`. That workflow publishes only when the Pages source is **GitHub Actions**. Use one source. `npm run deploy` is the branch publish.

The site is served at **https://gaaditracker.in** (custom domain set in **Settings → Pages → Custom domain**). The address is set in `src/data/site-url.mjs`:

```js
export const SITE_ORIGIN = 'https://gaaditracker.in';
export const BASE_PATH = '';   // '/gaaditracker.github.io' if the custom domain is removed
```

Every link, canonical URL, sitemap entry, share image, `robots.txt` and `llms.txt` follows these two lines. In `.astro` files, write internal links with the `url()` helper from `src/data/site.ts` (`href={url('/pricing')}`); in Markdown use relative links (`../pricing/`).

Locally the dev server runs at http://localhost:4321/.

### DNS for gaaditracker.in


At the domain registrar, the apex `gaaditracker.in` needs **only** GitHub's four A records: 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153 (remove any parking or forwarding records), and `www` a CNAME to `hangoutech.github.io`. Once GitHub has issued the certificate, tick **Enforce HTTPS** in Settings → Pages.
