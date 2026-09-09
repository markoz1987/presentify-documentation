# Presentify documentation

Public-facing documentation for the Presentify educator app. Currently one
deliverable: the **Presentify Educator Handbook**, a single-page web guide
covering app version 2.3.

## Layout

```
public/
├── index.html     the whole handbook: markup, styles and scripts in one file
└── img/           78 app screenshots, WebP, referenced by index.html
amplify.yml        AWS Amplify build spec + cache and security headers
```

`index.html` is 132 KB (about 31 KB gzipped) and has no build step and no
dependencies. Screenshots load lazily, so the first paint does not wait on
2.3 MB of images.

## Editing

Open `public/index.html` and edit it directly. The structure is stable:

- One `<section class="chapter" id="...">` per chapter, numbered in the
  left rail (`nav.rail`). Adding a chapter means adding both.
- Screenshots live in `<div class="strip">` blocks — add a `<figure>` with
  a `<span class="shot" data-cap="...">` wrapper and the lightbox picks it
  up automatically.
- Search, the "on this page" rail and the lightbox build their indexes from
  the DOM at load, so new headings and images need no registration.
- Callouts: `<div class="note">`, plus the `tip`, `warn`, `stop` and
  `newbox` variants. Version badges: `<span class="tag new">`.

To replace a screenshot, drop a new WebP into `public/img/` under the same
filename. Keep the filenames stable — they are cached for a year.

## Video walkthroughs

The eleven Vimeo clips are click-to-load: nothing is requested until a
reader presses play. If the host blocks third-party frames (Amplify does
not, but the Claude artifact preview does), the cards fall back on their
own to an "Open on Vimeo" link. The `Content-Security-Policy` in
`amplify.yml` allows `player.vimeo.com` in `frame-src` — remove that and
the players stop working.

## Deploying

Amplify Hosting builds this repo on every push to `main` and reads
`amplify.yml` from the repo root. There is nothing to run locally.

For S3 + CloudFront instead, sync `public/` and set the two cache policies
from `amplify.yml` by hand: a year on `img/`, no-cache on `index.html`, and
invalidate only `/index.html` after a deploy.
