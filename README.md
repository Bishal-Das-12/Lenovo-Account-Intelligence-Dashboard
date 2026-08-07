# Lenovo · Account Intelligence Dashboard

Static, single-page dashboard covering the Lenovo R&D and Activation account
portfolios — headcount, IT-headcount growth, buying-intent signals and cohort
filtering. Powered by [Sprouts.ai](https://sprouts.ai).

## Layout

```
index.html                      the dashboard — markup, styles, data and logic in one file
assets/sprouts-logo.svg         Sprouts lockup, dark (used in the footer)
assets/sprouts-logo-white.svg   Sprouts lockup, inverted (used in the dark header)
assets/favicon.svg
vercel.json                     static hosting config + cache headers
```

There is no build step and no dependencies. `index.html` is served as-is.

## Local preview

`fetch` is blocked on `file://`, so serve over HTTP to get the in-place
auto-refresh behaviour:

```sh
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploying to Vercel

1. Push this repo to GitHub.
2. In Vercel, **Add New → Project** and import the repo.
3. Framework preset: **Other**. Leave build command and output directory empty —
   this is a plain static site and `vercel.json` handles the rest.
4. Deploy.

Or from the CLI:

```sh
npx vercel --prod
```

`vercel.json` marks the HTML `must-revalidate` so a redeploy is picked up on the
next refresh tick, and caches `assets/` for a day.

## Auto-refresh

The page re-reads its own `DATA` block every 60 seconds and re-renders only when
the payload changed, so active filters and open panels survive. It pauses while
the tab is hidden and catches up on return; the footer carries a last-updated
stamp and a pause control.

The data is compiled into `index.html`, so on Vercel the numbers only change when
a new version is deployed — the refresh is what makes an open dashboard pick that
up without anyone reloading.

## Updating the data

Regenerate `index.html` with the new `let DATA = {…};` line and push. The refresh
loop looks for that exact single-line form, so keep the payload on one line.

## Sprouts logo

Both SVGs under `assets/` are derived from the official vector at
`https://sprouts.ai/sprouts-logo.svg` — geometry untouched. Only the fills were
changed, to the monochrome treatment:

| file | squircle | glyph | wordmark | used on |
| --- | --- | --- | --- | --- |
| `sprouts-logo.svg` | `#1A1A1A` | white | `#1A1A1A` | footer (light) |
| `sprouts-logo-white.svg` | white | `#161616` | white | header (dark) |

The Figma drop-shadow filter and the green brand gradient (`#00D95B` → `#00913D`)
were dropped, and the viewBox was tightened to the lockup bounds
(`6 2 141.18 32`). To go back to the brand green, re-add the gradient on the
squircle path.
