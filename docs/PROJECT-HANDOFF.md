# Project Handoff — The Blind Guy of Wenatchee Landing Page

**Purpose of this doc:** a portable, paste-into-another-chat summary of everything built in this
project, the live infrastructure state, decisions made, and what's outstanding. Written for
**Spotted Fox Digital** (marketing agency running Google Ads for many clients).

---

## TL;DR
A conversion-focused, single-page **Google Ads landing page** for **The Blind Guy of
Wenatchee** (window treatments, Wenatchee WA). It is **live, on a branded HTTPS subdomain,
auto-deploying from GitHub, and capturing + emailing leads.** The build was also generalized
into a **reusable template + playbook** so the agency can repeat it for future clients.

- **Live URL:** https://wenatchee.blindguyoftricities.com
- **Repo:** `spottedfoxdigital/Joshua-workspace`, branch `claude/determined-franklin-c2skc2` (draft **PR #1**)
- **Netlify site:** `blindguy-wenatchee`

---

## Deliverables & where they live
| Deliverable | Location |
|---|---|
| Live landing page | `landing-pages/blindguy-wenatchee/index.html` → https://wenatchee.blindguyoftricities.com |
| Privacy policy | `landing-pages/blindguy-wenatchee/privacy.html` → `/privacy` |
| Optimized photos (9) + favicon set | `landing-pages/blindguy-wenatchee/images/`, `…/favicon*` |
| **Reusable deployment playbook** | `docs/DEPLOYMENT-WORKFLOW.md` |
| **Clone-and-fill starter template** (33 placeholders) | `_template/` (+ `_template/README.md`) |
| Agency index / quickstart | `README.md` |

---

## Live infrastructure state (as configured)
**Hosting / deploy**
- Netlify site `blindguy-wenatchee`, **git-linked** to the repo.
- **Production branch:** `claude/determined-franklin-c2skc2` · **Build command:** (none) · **Publish dir:** `landing-pages/blindguy-wenatchee` (also in root `netlify.toml`).
- **Auto-deploys** on every push (~30s). No manual uploads.

**Domain / SSL**
- Custom domain **`wenatchee.blindguyoftricities.com`** (auto-set as Primary in Netlify).
- DNS at **GoDaddy** (external DNS, nameservers unchanged):
  - **CNAME** `wenatchee` → `blindguy-wenatchee.netlify.app`
  - **TXT** `subdomain-owner-verification` = `63dea257ae8d0914b8a3156722eb7220`
- **SSL:** Let's Encrypt, **active**. The `.netlify.app` URL 301s to the custom domain.

**Lead capture (Netlify Forms)**
- Two forms: **`estimate-hero`** and **`estimate-contact`** (Name, Phone, Email, product select) — plain static HTML with `data-netlify="true"`, hidden `form-name`, honeypot (`website`), and hidden UTM/gclid/fbclid capture.
- Submit via **AJAX** → inline "Thank You" (no redirect).
- **Form detection is ENABLED** (was off by default; required enabling + a redeploy).
- **Notifications go to:** `tristaff@blindguy.com` **and** `nickdrake@blindguy.com` (form-submission notifications). *(Joshua's test address was removed.)*
- **Verified working:** POST returns 200, entries save to the Forms dashboard, emails deliver.

**Tracking**
- GTM container **`GTM-KRK5NSR`** (shared with main site). `dataLayer` events: `generate_lead`, `click_to_call`, each tagged `page_type: landing_page` so LP conversions filter apart from the main site. UTM/gclid/fbclid captured into the lead.

---

## Client / brand facts (baked into the page)
- **Business:** The Blind Guy of Wenatchee (parent: The Blind Guy, blindguyoftricities.com)
- **Phone:** (509) 980-1402 · **Contact email:** tristaff@blindguy.com
- **Service area:** Wenatchee & the Wenatchee Valley, WA
- **Brand:** primary red `#FE0006` (hover `#C70005`), near-black `#0A0A0A`; fonts **Montserrat** (headings) + **Ubuntu** (body); logo embedded as base64; tagline "We Got You Covered."
- **Reviews:** 3 verbatim 5.0 Google reviews (Steve & Mary Winter, Lise Pugsley, Corinne Loyd); rating badge links to the Google Business Profile.
- **Favicon:** the brand's window-and-figure mark (cropped from the logo) on a white tile with red border.

---

## Status — done ✅
- [x] Page built (brand-accurate, no-nav single conversion path, dual lead forms, click-to-call)
- [x] Real photos added via GitHub upload, optimized 17 MB → 1.3 MB
- [x] Contact email + privacy policy + footer links
- [x] Favicon (from brand logo)
- [x] Voice cleanup (removed em-dashes + AI-tell words per the client's voice guide)
- [x] Redundant CTA buttons removed; product buttons changed "Explore" → "Get a Free Quote"
- [x] Git auto-deploy, custom domain, SSL
- [x] Netlify Forms working + notifications to the two client recipients
- [x] Reusable playbook + template + README

## Status — outstanding / next ⬜
- [ ] **Point Google Ads final URL** → `https://wenatchee.blindguyoftricities.com` (then confirm GA4/Ads conversions fire in GTM). *Hold spend until a real lead + email delivery is confirmed.*
- [ ] (Optional) **Merge PR #1 to `main`** so the default branch has everything.
- [ ] (Optional) **Per-client Netlify migration** — replace the hardcoded root `netlify.toml` with per-site base directories so each new client is independent (needs a coordinated Netlify config step).
- [ ] (Optional) **Intake checklist doc** — the list of assets to request from each new client.
- [ ] (Optional) **Custom-formatted lead email** ("New Lead: name, phone, email") via Zapier/Make or a Netlify Function (Netlify's native email can't be reformatted).
- [ ] (Optional) Hero background lifestyle photo; delete the test form entries.

---

## How this was built — the AI workflow (see `docs/DEPLOYMENT-WORKFLOW.md` for full detail)
Three actors, with an explicit hand-off loop:
- 🤖 **Claude Code** (cloud agent with the repo) — builds/edits the page, optimizes images, writes copy/privacy/favicon, manages git/PR, and **writes the browser-agent prompts**. Cannot reach the internet or log into dashboards.
- 🌐 **Claude for Chrome** (browser plugin) — executes all **Netlify/GoDaddy dashboard + live-testing** work in the user's authenticated browser and **reports back**. Cannot see the repo.
- 👤 **Human** — decisions, credentials, DNS, approvals, launch; the courier passing prompts/reports between the two.

**Loop:** Claude Code writes a prompt → human pastes into Claude for Chrome → it executes & reports → human pastes report back → Claude Code interprets & iterates.

## Top lessons / gotchas (that cost time)
1. **Chat-pasted images don't reach Claude Code** — upload to the GitHub repo instead.
2. **Drive originals too big** to pull through the connector — GitHub upload sidesteps it.
3. **Browser cache** makes deploys look broken — verify via Deploy Preview URL / hard refresh / Incognito.
4. **Netlify deploys the production branch** — must point at where the work lives, or it fails/serves stale.
5. **Netlify form detection is OFF by default** — enable it, then redeploy.
6. **Test forms on production**, not deploy-preview URLs.
7. **Form-submission notifications ≠ deploy notifications** (separate sections).
8. **Custom domains often need a TXT** (ownership) in addition to the CNAME.
9. **Claude Code can't reach the internet** by default — that's why the Chrome agent handles dashboards (or enable an egress allowlist on the environment).
10. **Netlify's native lead email isn't customizable** — add Zapier/Make/Function for custom formatting.

---

## Quick pointers for a new chat
- To **continue this project:** the repo + branch above have everything; the live site auto-deploys on push to that branch.
- To **start a new client:** copy `_template/` → `landing-pages/<client-city>/`, fill the `{{PLACEHOLDERS}}` (see `_template/README.md`), then follow `docs/DEPLOYMENT-WORKFLOW.md`.
