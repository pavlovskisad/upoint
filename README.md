# upoint

Landing page for upoint, a Ukrainian lifestyle club in Vienna.

Single file, no build, no dependencies.

```
open index.html          # that's it
npx serve .              # if you want a local server
```

## Deploy

Static site, zero build. On Vercel: import the repo, framework preset **Other**, leave
build command and output directory empty. `vercel.json` sets the cache headers; everything
else is default.

`og:url`, `og:image` and the canonical `<link>` in `index.html` are absolute URLs — they have
to be, because scrapers do not resolve a relative `og:image` — and they point at
`https://www.upoint.club/`. That is the host Vercel actually serves: the apex `upoint.club`
308-redirects to `www`. If you make the apex primary in Vercel instead, change these three
lines to match, or link previews chase a redirect for no reason.

```
index.html            the whole site
favicon.svg           tab icon, cropped from the mark
apple-touch-icon.png  180×180, iOS home screen
og.png                1200×630, link previews
robots.txt            allow all
vercel.json           headers only
```

The three image files are generated from the mark path in `index.html`, not drawn by hand.
If the mark changes, regenerate them rather than editing them.

See `CLAUDE.md` for the concept, the growth rules, the code map, and open questions.
