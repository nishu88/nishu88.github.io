# nishu88.github.io

Personal blog. Plain static HTML — no Jekyll, no build step, no dependencies.
What is in this folder is byte-for-byte what gets served.

**Live:** <https://nishu88.github.io/>
**Post:** <https://nishu88.github.io/blog/aws-ai-league-cisco-2026/>

```
index.html                                 post list + stats strip
blog/aws-ai-league-cisco-2026/index.html   the write-up (12 figures, ~2.6k words)
assets/css/style.css                       one stylesheet, shared by every page
assets/img/                                12 images, ~2 MB total
.nojekyll                                  tells GitHub Pages to serve files as-is
```

---

## Preview locally

```sh
python3 -m http.server 8000     # http://localhost:8000
```

Analytics deliberately do nothing on `localhost`, so previewing never inflates your numbers.

## Deploy an update

Pages is already configured: **source `master` / root**, HTTPS enforced, built automatically
on push. There is no workflow file because there is nothing to build.

```sh
git add -A && git commit -m "..." && git push origin master
```

> **The CDN caches for 10 minutes** (`cache-control: max-age=600`). After a push the build
> finishes in ~30s but the old page can linger at the edge. A hard reload (⌘⇧R) bypasses it;
> `curl -H 'Cache-Control: no-cache'` does the same from the terminal.

---

## What the pages do

### Theme — dark by default, with a toggle

The **dark** palette is defined on bare `:root`; `:root[data-theme="light"]` overrides it.
There is deliberately no `prefers-color-scheme` query, so the site opens dark even for a
reader whose OS is set to light — that is what "default to dark" means.

The toggle sits in the nav and stores the choice in `localStorage`. A small script in each
page's `<head>` applies the saved theme **before first paint**, so someone who chose light
never sees a dark flash on navigation. Every storage call is wrapped in try/catch, since
`localStorage` throws in private windows.

### Lightbox

Every `figure img` is clickable and opens full-screen on a dark backdrop with its caption.
Vanilla JS, no library. Images are focusable and announce as buttons; Enter/Space opens,
Esc or any click closes, Tab is trapped inside the dialog, and focus returns to the image
you came from. Background scroll locks while open. The overlay stays dark in both themes —
a light backdrop washes out screenshots.

### Stats strip

Five tiles on the home page. **Posts / Words / Latest** are static. **Views / Readers** are
fetched live and stay hidden if the request fails, so the page never shows `—`, `0`, or a
broken tile. Per-post counts also appear on the post card and in the post byline.

---

## Analytics

Two cookieless trackers, so **no consent banner is required**. Neither records anything on
`localhost` — a guard sets `window.__analytics` false there and both beacons are skipped.

| | What it gives you | Where |
|---|---|---|
| **GoatCounter** | views, unique visitors, referrers, countries, browsers, screen sizes | <https://nishu88.goatcounter.com> |
| **Cloudflare Web Analytics** | the same basics plus real-user Core Web Vitals | dash.cloudflare.com → Analytics & Logs → Web Analytics |

Cloudflare is registered against the **hostname** `nishu88.github.io` — not a path. Its free
tier is dashboard-only, with no public API.

GoatCounter's public counter endpoint needs no token and is what feeds the tiles:

```
https://nishu88.goatcounter.com/counter/TOTAL.json                     → whole site
https://nishu88.goatcounter.com/counter/%2Fblog%2F<slug>%2F.json       → one page
→ {"count":"6", "count_unique":"6"}
```

That endpoint exposes **only those two numbers**. Referrers, countries and browsers need an
authenticated API token, which cannot go in a public page. To surface them, make the
GoatCounter dashboard public in its settings and link to it — the dashboard is private today,
which is why there is no "Full stats" link.

---

## Add a post

1. Copy `blog/aws-ai-league-cisco-2026/` to `blog/<new-slug>/`. **The folder name is the URL.**
2. Edit `index.html`: `<title>`, the `description` / `og:` meta, the masthead, the body.
3. Add a card to the `.cards` list in the root `index.html`, and bump the Posts / Words /
   Latest tiles.
4. Keep all six script blocks. One sits in the `<head>` (applies the saved theme before
   first paint); the other five are at the bottom — theme toggle, lightbox, the
   `window.__analytics` localhost guard, the beacon injector, and the view counter.
   Nothing is shared between pages except the stylesheet, so each page carries its own copy.

> `.nojekyll` is an empty file and some tools quietly delete it. It is tracked in git and
> serving fine, but if it ever disappears locally, `touch .nojekyll` puts it back.

## Images

Web-sized copies, downscaled from the originals in `../_Cisco-ai-league/Images/`. Those are
8–9 MB screenshots and should not be committed here; everything in `assets/img/` is
35–430 KB. The two leaderboard shots are cropped to the podium rows only, because the full
screenshots list other participants from an internal Cisco leaderboard.

## History

This repo previously served a 2019 college project ("College Results 2019"). It was replaced,
not deleted — all 44 files are in history at commit `48fe13c` and come back with:

```sh
git checkout 48fe13c -- .
```
