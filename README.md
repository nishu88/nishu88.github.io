# nishu88.github.io

Personal blog. Plain static HTML — no Jekyll, no build step, no dependencies.
What is in this folder is byte-for-byte what gets served.

**Live:** <https://nishu88.github.io/>

Eight posts: the 2026 AWS AI League write-up, and a backfilled archive of seven
college competitions from September 2018 to November 2019.

```
index.html                                 post list, grouped by year, + stats strip
blog/aws-ai-league-cisco-2026/             the 2026 write-up (12 figures, ~2.6k words)
blog/two-we-didnt-win-2018-2019/           Techathlon + India Police Hackathon
blog/blockchain-summit-juincubator-2019/   JUIncubator / IBM, 2nd place
blog/hackwell-jssate-2019/                 Hackwell 1.0, Honeywell
blog/slac-amrita-2019/                     SLAC 2019, GE Healthcare
blog/business-marathon-esummit-2019/       E-Summit '19 Business Marathon
blog/ingenius-pesit-south-2018/            inGenius 2k18
blog/gazing-with-ml-phaseshift-2018/       Phase Shift 2018
assets/css/style.css                       one stylesheet, shared by every page
assets/img/                                12 images for the 2026 post, ~2 MB
assets/img/old_hacks/                      18 images for the archive posts, ~4 MB
view-baselines.txt                         the seeded half of each post's view count
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

Five tiles on the home page. **Posts / Words / Latest** are static and hand-edited.
**Views / Readers** are fetched live and stay hidden if the request fails, so the page never
shows an em dash, a `0`, or a broken tile. Per-post counts also appear on each post card and
in the post byline.

Posts are listed under an `<h2 class="yr">` per year, with a separate `.cards` container for
each. The year heading must stay **outside** `.cards`: that container draws its hairline
dividers with `gap:1px` over a `--border` background, so any direct child gets a rule around
it.

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
3. Add a card to the right year's `.cards` list in the root `index.html` (newest first —
   there is no sort), and bump the Posts / Words / Latest tiles. Each card carries a
   `<span class="cv" hidden></span>`; a loop reads every card's own `href` to fetch its
   count, so no per-card id needs inventing.
4. Keep all six script blocks. One sits in the `<head>` (applies the saved theme before
   first paint); the other five are at the bottom — theme toggle, lightbox, the
   `window.__analytics` localhost guard, the beacon injector, and the view counter.
   Nothing is shared between pages except the stylesheet, so each page carries its own copy.

> `.nojekyll` is an empty file and some tools quietly delete it. It is tracked in git and
> serving fine, but if it ever disappears locally, `touch .nojekyll` puts it back.

## View counts

The seven archive posts display **a fixed baseline plus their live GoatCounter count**. The
baselines are random 20–80 values chosen once at publish time and written down in
[`view-baselines.txt`](view-baselines.txt); their sum is also added to the home page's
Views / Readers tiles so the listing and the totals agree. The 2026 AWS AI League post has no
baseline and shows its raw count.

The baseline is not a measurement. It is recorded in a public file at the site root so anyone
who wonders where a number came from can check.

## Images

Web-sized copies. Everything served is 35–430 KB; originals are not committed.

- `assets/img/` — the 2026 post, downscaled from `../_Cisco-ai-league/Images/`. The two
  leaderboard shots are cropped to the podium rows, because the full screenshots list other
  participants from an internal Cisco leaderboard.
- `assets/img/old_hacks/` — the archive posts. Photographs, certificates and event posters
  from 2018–2019, plus two certificates extracted from a personal `ACHIEVEMENTS.docx`.

Everything in `old_hacks/` was processed the same way: EXIF **orientation baked into the
pixels first**, then resized with `sips -Z`, then all metadata stripped with
`exiftool -all=`. Order matters — stripping before baking loses the orientation flag and
leaves phone photos sideways. Stripping also removes capture timestamps and any GPS.

One promotional poster was deliberately left out: it carried two organisers' phone numbers.
Its facts are in the prose instead.

## History

### Backdated commits

The seven archive posts are committed with `GIT_AUTHOR_DATE` and `GIT_COMMITTER_DATE` set to
the date of the event each one describes, so the repository and the GitHub contribution graph
show them in 2018 and 2019. The writing itself was done in 2026. `git log` therefore reads
non-monotonically, with 2018–2019 commits sitting on top of 2026 ones; that is expected.

All commits are authored as
`Nishanth D Aluhonnu <29069343+nishu88@users.noreply.github.com>`. An earlier batch used a
work address that GitHub credits to a different account, and was rewritten with
`git filter-branch --env-filter` and force-pushed. **That rewrite changed every SHA in the
repository**, including the pre-2026 ones.

### The 2019 college project

This repo previously served "College Results 2019". It was replaced, not deleted — all 44
files survive at commit `c97e694`, the parent of the commit that introduced the blog:

```sh
git checkout c97e694 -- .
```

The SHA `48fe13c` quoted here before the rewrite no longer exists on `master`.
