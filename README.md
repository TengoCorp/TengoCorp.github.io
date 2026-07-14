# TengoCorp.github.io

Public marketing site for Tengo — a single static page, no build step.

**Live:** https://www.tengolife.com

## How it is served

GitHub Pages publishes this repo from the `main` branch, root directory.
Pushing to `main` deploys.

- `index.html` — the whole site (inline CSS + JS, no dependencies).
- `tengo-wordmark.svg` — brand asset. Not currently referenced by the page.
- `CNAME` — tells Pages the site's canonical hostname. **Do not delete**; Pages
  rewrites it from the repo settings, and losing it drops the custom domain.
- `.nojekyll` — serve files verbatim, skip Jekyll processing.

Because the org is on the GitHub Free plan, Pages requires this repo to be
**public**. It holds only the HTML already served publicly at the URL above —
nothing else belongs here. No credentials, no keys, no client data.

## Relationship to the product

This is the marketing shopfront only. The Tengo platform itself lives in
`portal-specs`, `portal-backend`, and `portal-frontend`, and is not deployed
from here.

## DNS

`www` is a CNAME to `tengocorp.github.io`; the apex (`tengolife.com`) uses A
records to GitHub's Pages IPs and redirects to `www`. Visitors always see
`www.tengolife.com` — Pages serves the site on that hostname directly and never
redirects to `*.github.io`.

Do **not** use the registrar's "URL Redirect Record" type for either host: it
rewrites the address bar, which is exactly what the CNAME setup avoids.
