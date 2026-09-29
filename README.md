# Blog

Plain static HTML. No Jekyll, no build step, no dependencies. What is in this folder
is exactly what gets served.

```
index.html                                        post list
blog/aws-ai-league-cisco-2026/index.html          the write-up
assets/css/style.css                              one stylesheet, shared by all pages
assets/img/                                       12 images, ~2 MB total
.nojekyll                                         tells GitHub Pages to serve as-is
```

## Preview locally

```sh
python3 -m http.server 8000
# then open http://localhost:8000
```

Opening `index.html` by double-clicking works too, but the server is closer to
how Pages will serve it.

## Publish

1. Create a **public** repo named `nishu88.github.io` on github.com. Do not add a README.
   (Pages on a private repo needs a paid plan. A different repo name also works, but then
   every page lives under `/reponame/` and the root-relative OG image URLs need updating.)
2. Push:
   ```sh
   git init -b main
   git add .
   git commit -m "Blog: AWS AI League write-up"
   git remote add origin git@github.com:nishu88/nishu88.github.io.git
   git push -u origin main
   ```
3. **Settings → Pages → Source: Deploy from a branch**, branch `main`, folder `/ (root)`.
4. Live in a minute or two at:
   - `https://nishu88.github.io/`
   - `https://nishu88.github.io/blog/aws-ai-league-cisco-2026/`

> Do not publish this from the `ai_league` repo. That one holds the finale prompt and the
> four Lambda tools, and enabling Pages there would force all of it public.

## Add a post

Copy an existing `blog/<slug>/index.html`, edit the content, and add a card to `index.html`.
The folder name is the URL. Everything shares `assets/css/style.css`.

## Theme

**Dark by default**, regardless of the reader's OS setting. Colours are CSS custom
properties: the dark palette sits on bare `:root`, and `:root[data-theme="light"]`
overrides it. The toggle in the nav sets that attribute and remembers the choice in
`localStorage`; a tiny script in each page's `<head>` applies it before first paint, so
a light-mode reader never sees a dark flash. Type is Bricolage
Grotesque for headings, Source Serif 4 for body, JetBrains Mono for figures and code,
all from Google Fonts.

## Images

Web-sized copies, downscaled from the originals in `../_Cisco-ai-league/Images/`, which are
8–9 MB screenshots and should not be committed here. The two leaderboard shots are cropped
to the podium rows only; the full screenshots list other participants from an internal
Cisco leaderboard.

## Analytics

Both `index.html` and each post carry two cookieless trackers. They are inert until you
replace the placeholders, and they never fire on `localhost`.

**GoatCounter** — free for personal use, open source.
1. Sign up at <https://www.goatcounter.com> and pick a code, e.g. `nishu88`.
2. Replace `GOATCOUNTER_CODE` in every HTML file with that code.
3. Dashboard: `https://<code>.goatcounter.com`.

**Cloudflare Web Analytics** — free, no DNS change needed.
1. <https://dash.cloudflare.com> → Analytics & Logs → Web Analytics → Add a site.
2. Enter `nishu88.github.io`. Cloudflare shows a beacon snippet containing a token.
3. Replace `CLOUDFLARE_TOKEN` with that token.

```sh
# swap both in one go
grep -rl GOATCOUNTER_CODE . --include=*.html | xargs sed -i '' 's/GOATCOUNTER_CODE/nishu88/g'
grep -rl CLOUDFLARE_TOKEN . --include=*.html | xargs sed -i '' 's/CLOUDFLARE_TOKEN/<your-token>/g'
```

Neither uses cookies or stores personal data, so no consent banner is required. Neither
tells you *who* is reading, only aggregates: page, country, referrer, device. For "who",
LinkedIn's own post analytics is far better — it shows job titles and companies.

Remember to copy the analytics block into any new post page.
