# grove-site

Public site for **The Grove**, hosted at `grove.cedarridge.capital`.

- `index.html` — the homepage / journey site (single-file, ~11MB; treat as an opaque built asset)
- `privacy.html` — Privacy Policy (linked from the Google OAuth consent screen)
- `terms.html` — Terms of Service
- `VERIFICATION.md` — the Google OAuth verification runbook (not deployed)

## Deploy
Static hosting under `grove.cedarridge.capital`. Cloudflare Pages or GitHub Pages both work:
point the subdomain's DNS at the host, serve this directory. Privacy/Terms must be reachable at
`/privacy.html` and `/terms.html` for OAuth verification.
