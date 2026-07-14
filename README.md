# TengoCorp.github.io

Public marketing site for Tengo — a single static page, no build step.

**Current URL:** https://tengocorp.github.io/ — a **pre-launch preview**.
The custom domain is deliberately *not* attached yet.

**Launch target:** `www.tengolife.com` (see "Going live" below).

## How it is served

GitHub Pages publishes this repo from the `main` branch, root directory.
Pushing to `main` deploys.

- `index.html` — the whole site (inline CSS + JS, no dependencies).
- `tengo-wordmark.svg` — brand asset. Not currently referenced by the page.
- `.nojekyll` — serve files verbatim, skip Jekyll processing.

## Going live on www.tengolife.com

Two steps, in this order:

1. **DNS** (Namecheap → Advanced DNS). Add `CNAME  www → tengocorp.github.io.`
   and four `A` records on `@` → `185.199.108.153`, `.109.153`, `.110.153`,
   `.111.153`. Delete the default `CNAME www → parkingpage.namecheap.com` and
   any **URL Redirect Record** on `@`.
   **Leave the `MX` records alone** — the domain carries live Google Workspace
   email (`info@tengolife.com`).
2. **Pages** → Settings → Custom domain → `www.tengolife.com`, then tick
   *Enforce HTTPS* once the certificate provisions.

Pages then serves the site **on** `www.tengolife.com` — the browser URL stays
there and never flips to `*.github.io`. The redirect runs the other way:
`tengocorp.github.io` will 301 to the custom domain.

Do **not** use the registrar's "URL Redirect Record" type for either host — that
one *does* rewrite the address bar, which is precisely what the CNAME setup
avoids.

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
