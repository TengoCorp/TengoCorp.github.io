# Go-Live Runbook — www.tengolife.com

Handoff for **cb@tengolife.com** (repo admin). This takes the site from its
current preview URL to the live custom domain.

- **Repo:** `TengoCorp/TengoCorp.github.io` (public, GitHub Pages from `main` / root)
- **Preview URL now:** https://tengocorp.github.io/ — already live, no custom domain attached
- **Launch target:** `www.tengolife.com`
- **DNS host:** Namecheap (nameservers `dns1/dns2.registrar-servers.com`)

Do the two halves **in this order: DNS first, GitHub second.** If you attach the
domain in GitHub before DNS resolves, GitHub marks it "unverified" and the TLS
certificate can't issue — it looks broken and you just end up waiting anyway.

---

## Step 0 — Accept the repo invite

There's a pending invitation to `cb@tengolife.com`. Accept it (email link, or
https://github.com/TengoCorp/TengoCorp.github.io/invitations) before anything
below. You need admin on the repo to change Pages settings.

---

## Step 1 — DNS at Namecheap

Namecheap → **Domain List** → `tengolife.com` → **Manage** → **Advanced DNS** →
**Host Records**.

Add these five records:

| Type          | Host  | Value                    | TTL       |
| :------------ | :---- | :----------------------- | :-------- |
| CNAME Record  | `www` | `tengocorp.github.io.`   | Automatic |
| A Record      | `@`   | `185.199.108.153`        | Automatic |
| A Record      | `@`   | `185.199.109.153`        | Automatic |
| A Record      | `@`   | `185.199.110.153`        | Automatic |
| A Record      | `@`   | `185.199.111.153`        | Automatic |

(The four A records point the bare apex `tengolife.com` at GitHub Pages; GitHub
then redirects apex → `www`. The four IPs are GitHub's published Pages
addresses — confirm current values any time with `dig +short A tengocorp.github.io`
or GitHub's docs.)

**Then delete** these if present:
- the default `CNAME  www → parkingpage.namecheap.com`
- any **URL Redirect Record** on `@` or `www`

### Two things you must NOT do

1. **Do not touch the `MX` records.** `tengolife.com` runs live Google Workspace
   email (`MX → smtp.google.com`) — that's what `info@tengolife.com` and every
   `@tengolife.com` address depend on. A/CNAME changes don't affect it, but
   "reset all records" would kill company email. Leave MX (and the
   `google-site-verification` TXT) alone.
2. **Do not use Namecheap's "URL Redirect Record" type.** That record *frames or
   bounces* visitors and rewrites the browser address bar — the opposite of what
   we want. Use a plain **CNAME** only. (See "Why the URL won't change" below.)

DNS can take anywhere from a few minutes to a couple hours to propagate. Check with:

```
dig +short www.tengolife.com      # should list the 185.199.x.153 IPs (via GitHub)
dig +short A tengolife.com        # should list the four 185.199.x.153 IPs
```

---

## Step 2 — GitHub Pages (do after DNS resolves)

Repo → **Settings** → **Pages**:

1. Under **Custom domain**, enter `www.tengolife.com` → **Save**.
   - This writes a `CNAME` file back into the repo automatically. That's
     expected — leave it.
2. GitHub runs a **DNS check**. Wait for **"DNS check successful."** If it says
   unverified, DNS hasn't propagated yet — wait and re-check; don't change
   anything.
3. GitHub provisions a **Let's Encrypt TLS certificate** for the domain. This can
   take a few minutes up to ~an hour. The **Enforce HTTPS** checkbox is greyed
   out until the cert is ready.
4. Once available, tick **Enforce HTTPS**. Done.

---

## Step 3 — Verify

```
curl -sS -o /dev/null -D - https://www.tengolife.com/      # expect: HTTP 200, no Location header
curl -sS -o /dev/null -D - https://tengocorp.github.io/    # expect: 301 → https://www.tengolife.com/
curl -sS -o /dev/null -D - https://tengolife.com/          # expect: 301 → https://www.tengolife.com/
```

The site should load at `https://www.tengolife.com` and **stay there** in the
address bar. The github.io URL and the bare apex both redirect *to* www.

---

## Why the URL won't change (the thing Rich asked about)

A GitHub Pages **custom domain is not a redirect** — Pages *serves* the site on
`www.tengolife.com` and holds a certificate for it, so the browser stays on that
hostname on every page and reload. The only redirect goes the other direction:
`tengocorp.github.io` → `www.tengolife.com`. The one setup that *would* rewrite
the address bar is the registrar's "URL Redirect Record," which is exactly why we
don't use it (Step 1).

---

## Rolling it back (if ever needed)

Repo → **Settings** → **Pages** → clear the **Custom domain** box → **Save**.
That removes the `CNAME` file and the site goes back to serving at
`tengocorp.github.io`. No Namecheap change required to revert; the DNS records
can stay in place harmlessly.

---

## Known pre-launch gaps (not blockers, owner: Belinda / comms)

These affect how the site looks when shared, not whether it loads:

- `apple-touch-icon.png` and `tengo-og.png` are **referenced in `index.html` but
  don't exist yet** (the files were never added). Until they're committed to the
  repo root, the iOS home-screen icon and the link-preview image (LinkedIn /
  Slack / iMessage / X) will be blank or broken. The favicon works — it's inline
  SVG.
- The OG/Twitter tags use `https://tengolife.com/` (apex) as the canonical URL,
  while the site serves on `www`. Harmless (apex redirects), but worth aligning
  before launch. **Don't change these meta tags without Belinda's sign-off.**
