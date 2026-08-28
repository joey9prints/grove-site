# The Grove — Google OAuth Verification Pack

Everything needed to submit The Grove for Google OAuth verification. `gmail.readonly` is a
**restricted** scope, so this is the full path: brand verification **+ a CASA security assessment**.
Budget several weeks; run the app in **Testing mode** meanwhile so friends can onboard now (the
switch to Verified is invisible to them — same code, same client).

---

## 0. Prerequisites (do these first)

1. **Own + verify the domain.** In Google Search Console, verify `cedarridge.capital` (add a DNS TXT
   record). Everything below must live under it.
2. **Host the site** at `grove.cedarridge.capital`:
   - `/` → `index.html` (the homepage — the journey site)
   - `/privacy.html` → Privacy Policy
   - `/terms.html` → Terms of Service
3. **Move the OAuth redirect onto your own domain** (strongly recommended — Google scrutinizes domain
   consistency for restricted scopes). Point a custom domain at the Railway **grove-api** service, e.g.
   `api.grove.cedarridge.capital`, and set the redirect to
   `https://api.grove.cedarridge.capital/grove/v1/oauth/gmail/callback`.
   Then update in three places so they match exactly:
   - Railway env `GMAIL_OAUTH_REDIRECT_URI` (both services)
   - Google Cloud Console → Credentials → your OAuth client → Authorized redirect URIs
   - (the current `…up.railway.app/...callback` works functionally, but an owned‑domain redirect makes
     the review cleaner.)

---

## 1. OAuth consent screen (APIs & Services → OAuth consent screen)

| Field | Value |
|---|---|
| App name | **The Grove** |
| User support email | `support@cedarridge.capital` (or your preferred) |
| App logo | The Grove mark (120×120+ PNG, no rounded corners baked in) |
| Application home page | `https://grove.cedarridge.capital` |
| Privacy policy link | `https://grove.cedarridge.capital/privacy.html` |
| Terms of service link | `https://grove.cedarridge.capital/terms.html` |
| Authorized domain | `cedarridge.capital` |
| Developer contact email | your email |
| User type | **External** |

---

## 2. Scopes + justifications

Add exactly these scopes. For each, Google wants *why you need it* and *why nothing narrower works* —
copy these:

**`openid`, `.../auth/userinfo.email`** — Identify which mailbox the user connected, so their data is
tied to the right account. (Non‑sensitive.)

**`.../auth/gmail.readonly`** *(restricted)* —
> The Grove builds a private fundraising CRM from the user's own correspondence: it reads the messages
> between the user and their professional contacts to extract who the contacts are, which firms they're
> at, and the history and status of each conversation. Reading message bodies is essential — the
> narrower `gmail.metadata` scope exposes only headers and cannot provide the conversation content
> needed to summarize interactions or identify deal context. The Grove only reads; it never modifies,
> deletes, or moves mail.

**`.../auth/gmail.send`** *(sensitive)* —
> The Grove lets the user send a follow‑up email to a contact. The email is drafted in the app, the
> user reviews and edits it, and it is sent from their own account only when they tap Send. The Grove
> never sends autonomously. A narrower approach (a `mailto:` compose hand‑off) was insufficient because
> it cannot send from the user's account programmatically after explicit review, nor reliably log the
> sent message back to the CRM history.

---

## 3. Demo video (required for restricted scopes)

Record a short screen capture (English, unlisted YouTube link is fine). Google needs to see the OAuth
grant **and** how each scope is used. Script:

1. Show the app's URL/identity and start the Google connect flow.
2. **Show the Google consent screen** with the `gmail.readonly` and `gmail.send` scopes visible, and grant.
3. **`gmail.readonly` in use:** trigger an import / open a contact whose history was built from email —
   show that The Grove read the user's mail to populate contacts + conversation history.
4. **`gmail.send` in use:** open a draft, edit it, tap Send, and show it sending from the user's account.
5. Show the Privacy Policy link in the app/site and the disconnect/revoke control.

> You already have `The Grove.mp4` / `grove.mp4` in the project folder — but the verification video has
> to show the *OAuth consent screen and scope usage specifically*, so record a fresh one to that script
> rather than reusing a marketing cut.

---

## 4. CASA security assessment (because of `gmail.readonly`)

Restricted scopes require an annual **CASA (Cloud Application Security Assessment)**, Tier 2, by a
Google‑authorized third‑party assessor.
- Google emails you the CASA requirement after you submit for verification; you engage an assessor
  (e.g. from Google's approved list), who runs a scan + review against the OWASP ASVS.
- Cost is typically a few hundred to a few thousand dollars/year depending on assessor and tier.
- Prep that helps it go fast: TLS everywhere (done), encryption at rest (done — per‑tenant envelope
  encryption), least‑privilege scopes (done), a documented data‑deletion path (done — see Privacy §5),
  and no secrets in the client. The Grove's architecture already lines up with most ASVS items.

---

## 5. Submission steps

1. Finish §0 prerequisites (domain verified, site live, redirect on your domain).
2. Fill the consent screen (§1), add scopes (§2), attach the demo video (§3).
3. Under the consent screen, click **Publish app** → **Prepare for verification** → submit.
4. Respond to Google's emails; complete CASA when prompted.
5. **Meanwhile**, keep the app in **Testing** with your friends added as **Test users** (Audience →
   Test users, up to 100) so onboarding works today. When verification completes, publish to Production
   and the "unverified app" warning disappears for everyone — no code change.

---

## 6. Quick facts (for the forms)

- **Scopes:** `openid`, `email`, `gmail.readonly`, `gmail.send`
- **Redirect URI:** `https://api.grove.cedarridge.capital/grove/v1/oauth/gmail/callback` (target)
- **Home / Privacy / Terms:** `https://grove.cedarridge.capital` · `/privacy.html` · `/terms.html`
- **Data handling:** read‑only Gmail access to build the user's own CRM; send only on explicit user
  action; encrypted at rest per‑tenant; sub‑processor Anthropic (no model training on API data); no
  advertising; no sale; deletion within 30 days on request.
