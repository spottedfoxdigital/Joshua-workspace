# Landing Page Deployment Playbook
**Repeatable workflow for paid-ad landing pages (template: The Blind Guy of Wenatchee)**

This playbook captures the end-to-end process for shipping a conversion-focused,
single-page landing site for a Google Ads campaign — from brief to live, lead-capturing
page on a branded subdomain. It is deliberately explicit about **which AI surface does
what** and **where a human is required**, because the work spans two different AI tools
plus human decisions.

---

## 0. The three "actors" and how they hand off

There are **two distinct AI surfaces** in this workflow, plus you. Knowing which is which
is the whole game — most confusion comes from asking the wrong actor to do something it
physically can't.

| Actor | What it is | Superpower | Hard limits |
|------|------------|-----------|-------------|
| 🤖 **Claude Code** (this agent) | Claude running in an **ephemeral cloud container** that has the **git repo** cloned into it. | Builds/edits the page, optimizes images, writes the privacy policy & favicon, manages git/commits/PRs, and **writes the prompts for the browser agent**. | **Cannot reach the public internet** (can't open Netlify/GoDaddy, can't crawl live sites, can't log into anything). **Cannot see your screen.** Container is wiped between sessions — anything not committed is lost. |
| 🌐 **Claude for Chrome** (browser plugin) | Claude operating **inside your logged-in browser**. | Does anything that needs a **UI + your credentials**: Netlify dashboard, GoDaddy DNS, live form testing, reading dashboards/Network tab. | Only as reliable as the instructions you give it. It can't see the repo/code. Treat it as a careful junior operator. |
| 👤 **You (human)** | The decision-maker and credential-holder. | Approvals, brand/content decisions, anything requiring judgment, registrar/DNS access, final launch. | Time. The goal of this playbook is to minimize your manual clicking. |

### The hand-off loop (memorize this)
```
🤖 Claude Code  ──writes a prompt──▶  👤 You paste it into  ──▶  🌐 Claude for Chrome
        ▲                                                                  │
        │                                                                  │ executes in
        └────────── 👤 You paste the report back ◀──── reports results ◀───┘  Netlify/GoDaddy
```
**Why the split exists:** Claude Code can't log into Netlify or reach the internet from its
sandbox, and Claude-for-Chrome can't see the codebase. So Claude Code does all the
**code/repo** work and **authors browser instructions**; the Chrome agent does all the
**dashboard/UI** work in your authenticated browser and **reports back**; Claude Code
interprets the report and iterates. You are the courier and the approver.

> **Optional upgrade:** Claude Code's sandbox can be given outbound network access via the
> environment's **network policy** (Claude Code on the web → environment settings → allowlist
> domains like the client site, Google Fonts, etc.). If enabled, Claude Code can verify live
> deploys and DNS itself, reducing browser-agent round-trips. Default is locked down.

---

## Legend for the phases
- 🤖 = Claude Code (code/repo/AI generation)
- 🌐 = Claude for Chrome (browser automation)
- 👤 = Human (decision, credential, or content)

---

## Phase 1 — Intake & Discovery  👤 (with 🤖 organizing)

**Goal:** gather everything the build needs *before* writing code, so Phase 2 is one clean pass.

**👤 Human collects and hands to 🤖 Claude Code (upload as file attachments or to the repo):**
- [ ] **Creative brief / recommendation** (what the page should say/do, offers, sections)
- [ ] **Brand style guide** (colors as hex, fonts, logo files) — PDF is fine; Claude Code can extract logos/colors from it
- [ ] **Voice & tone guidelines** (so copy sounds human/on-brand, not generic AI)
- [ ] **Business facts:** legal name, service area/city, phone, **public contact email**, hours
- [ ] **Real Google reviews** (verbatim text + reviewer names) and the **Google Business Profile URL**
- [ ] **Tracking:** GTM container ID (and whether it's shared with the main site)
- [ ] **Domain plan:** the subdomain to launch on (e.g. `city.maindomain.com`) and **who manages DNS**
- [ ] **Photos:** real job photos beat stock. (See Phase 3 for the *correct* way to get them in.)

**⚠️ Pitfall:** Decide the **subdomain** now (e.g. `wenatchee.blindguyoftricities.com`) — it's
baked into the page's meta/OG tags and the campaign's final URL.

**Output:** a complete asset bundle in the repo / uploaded to the session.

---

## Phase 2 — Build the page  🤖 Claude Code

**Goal:** a self-contained, on-brand, conversion-optimized HTML page.

**🤖 Claude Code does:**
- [ ] Generate the page from the brief + brand guide (using the landing-page-generator skill or by cloning a prior page like `landing-pages/blindguy-wenatchee/` as a starter).
- [ ] Brand accuracy: exact hex colors, correct fonts (e.g. Google Fonts), **logo embedded as base64** (no external hotlink), tagline.
- [ ] **Conversion architecture:** no nav menu (single conversion path), every CTA anchors to the form, click-to-call phone in header/hero/footer.
- [ ] **Lead forms** (usually two — hero + bottom): Name, Phone, Email, product interest, plus a **honeypot** field and **hidden UTM/gclid/fbclid capture** fields.
- [ ] **Tracking:** GTM snippet, `dataLayer` events (`generate_lead`, `click_to_call`), `page_type: landing_page` so LP conversions can be filtered from the main site.
- [ ] **SEO/a11y:** `noindex,nofollow` (it's an ad LP), JSON-LD LocalBusiness, Open Graph, labels/alt/focus states, WCAG AA contrast.
- [ ] Real reviews section + ratings.

**👤 Human reviews:** copy tone, offers, brand feel. Give edits in plain language; 🤖 applies them.

**Output:** `landing-pages/<client-city>/index.html` committed to a feature branch.

---

## Phase 3 — Photos & assets  👤 + 🤖

**Goal:** real, optimized images on the page. **This phase has the single biggest "gotcha."**

> ### ⚠️ CRITICAL LESSON: how to get images to Claude Code
> - ❌ **Pasting images into the chat does NOT work.** Claude Code can *see* them but they
>   never land as files in its sandbox — it can't put them on the page.
> - ❌ **Google Drive originals are usually too big** (3–18 MB) to pull through the connector
>   without overflowing context.
> - ✅ **The reliable way:** upload the image files **directly into the GitHub repo** (GitHub
>   web UI → the page's `images/` folder → *Add file → Upload files* → commit to the feature
>   branch). Then 🤖 Claude Code pulls them with `git pull`.

**🤖 Claude Code then:**
- [ ] Optimizes every image (resize to ~1100px, compress to JPG/WebP). *On this build: 17 MB → 1.3 MB.* Fast load = better Ads Quality Score.
- [ ] Renames to clean, intentional filenames and wires them into the page with `alt` text, lazy-loading, and graceful fallback (broken/missing image hides instead of showing a broken icon).
- [ ] Builds a **contact sheet** to visually QA that each photo is in the right slot.

**👤 Human:** picks which photos go where (you can see Drive thumbnails; Claude Code can't reliably).

---

## Phase 4 — Finalize content  🤖 (with 👤 supplying specifics)

- [ ] 🤖 **Privacy policy page** (`privacy.html`) — tailored to the form data + GTM/Ads/Meta tracking; linked in footer. (Required for Google Ads.)
- [ ] 👤 **Contact email** + 🤖 wire it into footer & privacy policy.
- [ ] 🤖 **Favicon** — crop the brand mark from the logo, generate `.ico` + PNGs + `apple-touch-icon`, wire into `<head>` of every page.
- [ ] 🤖 **Voice cleanup** — remove "AI tells" per the voice guide: **no em-dashes**, cut words like "tailored/seamless/elevate," add contractions, lead with the benefit, add local references. (Leave verbatim customer reviews untouched.)
- [ ] 🤖 **CTA wording** — buttons should say the next step ("Get a Free Quote"), not "Explore."

---

## Phase 5 — Repo, PR, and the Netlify ↔ GitHub link  🤖 → 🌐

**Goal:** continuous deploy so every push goes live automatically (no more manual uploads).

**🤖 Claude Code:**
- [ ] Commit all work to the feature branch; push; open a **draft PR**.
- [ ] Add a `netlify.toml` at repo root with the publish directory:
      ```toml
      [build]
        publish = "landing-pages/<client-city>"
      ```

**🌐 → Switch to Claude for Chrome** (Claude Code writes this prompt, you paste it):
- [ ] Connect the Netlify site to the GitHub repo (Site config → Build & deploy → Link repository).
- [ ] Set **production branch**, **build command = blank**, **publish directory = `landing-pages/<client-city>`**.

> ### ⚠️ LESSON: production branch must match where the work lives
> If the work is on a feature branch (not `main`), Netlify will try to deploy `main`, fail
> ("deploy directory not found"), and you'll see the *old* site. Fix: set the **production
> branch** to the feature branch (or merge to `main` and deploy `main`). The publish
> directory must point at the subfolder that contains `index.html`.

**Result:** 🤖 pushes → site rebuilds in ~30s. No zip files, ever.

> ### ⚠️ LESSON: the "which version am I seeing?" cache trap
> After a deploy, browsers serve the **cached** old page. Always verify via the **Deploy
> Preview URL** (unique, never cached) or a **hard refresh (Cmd+Shift+R)** / **Incognito**.
> A one-glance tell beats checking every edit (e.g. "new build has no red header button").

---

## Phase 6 — Custom domain + SSL  🌐 + 👤

**Goal:** live on the branded subdomain with HTTPS.

**🌐 Claude for Chrome (in Netlify):**
- [ ] Domain management → **Add a domain** → `city.maindomain.com` → choose **external DNS** (do **not** switch nameservers to Netlify DNS).
- [ ] Capture the DNS records Netlify asks for and report them back.

**👤 Human / DNS team (at the registrar — e.g. GoDaddy):**
- [ ] **CNAME:** `city` → `<netlify-site>.netlify.app` (routes traffic)
- [ ] **TXT:** ownership-verification record Netlify provides (e.g. host `subdomain-owner-verification`, the given value) — **required to issue SSL when the root domain isn't in the Netlify account.**

> ### ⚠️ LESSONS
> - You usually need **two records** (CNAME *and* the ownership TXT), not just the CNAME.
> - Netlify **auto-promotes the custom domain to Primary** — that's fine; it's the correct
>   canonical URL. (It will 301 the `.netlify.app` URL to it once DNS is live.)
> - SSL **auto-issues** via Let's Encrypt once both records propagate (5–30 min). The
>   "couldn't provision cert" message is expected until then.

---

## Phase 7 — Lead capture (Netlify Forms)  🌐 + 👤

**Goal:** every submission is saved AND emailed to the right people.

> ### ⚠️ BIGGEST FORMS LESSON: detection is OFF by default
> Netlify now requires you to **explicitly enable form detection**, and it only registers
> forms on a deploy that happens **after** it's enabled. Steps:

**🌐 Claude for Chrome:**
- [ ] Forms → **Enable form detection**.
- [ ] **Trigger a fresh deploy** (Deploys → Trigger deploy) so the HTML is re-scanned.
- [ ] Confirm both forms now appear (e.g. `estimate-hero`, `estimate-contact`).
- [ ] **Form submission notifications** → add the email recipient(s). *Tip: during testing,
      route to ONE test inbox so you don't spam the client; swap to the real recipients once
      confirmed.*
- [ ] **Test on the PRODUCTION domain** (not a deploy-preview): submit both forms, confirm
      the inline "Thank You", and confirm the **POST returns HTTP 200** (DevTools → Network).
- [ ] Confirm entries land in the Forms dashboard + the notification email arrives.

> ### ⚠️ LESSONS
> - Test on the **production URL**, not `deploy-preview-…` (previews don't process forms the
>   same way → "Something went wrong" error).
> - The page submits via AJAX and shows an inline error if the POST isn't 2xx. A 404 = form
>   not registered (detection off / not redeployed). A 200 = working.
> - **Form submission notifications** ≠ **Deploy notifications** — they're separate sections.
>   Lead emails go under *Form submission notifications*.
> - Netlify's native email **can't be reformatted** (fixed subject/body). For a custom
>   subject like "New Lead: name, phone, email," add **Zapier/Make** or a **Netlify Function
>   + email API** between Netlify and the inbox.

---

## Phase 8 — Launch  👤

- [ ] 👤 Point the **Google Ads final URL** to `https://city.maindomain.com`.
- [ ] 👤 Confirm **GA4 / Google Ads conversion** tags fire in GTM, filtered by `page_type = landing_page`.
- [ ] 👤 Do a final live lead test end-to-end; confirm the client received it.
- [ ] 👤 (Optional) hero lifestyle photo, custom-formatted lead email, delete test form entries.
- [ ] 👤 **Hold ad spend** until a real submission + email delivery is confirmed.

---

## Reusable Claude-for-Chrome prompt template

Every browser hand-off should follow this shape (it's why ours were reliable):

```
Task: <one-line goal>

Context: Netlify site <site-name>, live at <url>. <1–2 lines of relevant context>.

Steps:
1. <explicit click-by-click step>
2. ...
(Include exact values: domains, emails, branch names, field labels.)

Report back: <the specific facts you need — statuses, lists, error text>.

Guardrails:
- Do NOT change <settings you want protected: domains, env vars, build, primary domain>.
- If <X is ambiguous / a form/entry doesn't appear / a setting you didn't specify>, STOP and
  report rather than guessing.
```
**Why this works:** explicit values prevent wrong guesses; "report back" turns the agent into
a sensor that feeds Claude Code; guardrails + "stop and report" prevent unwanted changes. You
then paste the report to Claude Code, which decides the next move.

---

## Master checklist (copy this per project)

```
INTAKE (👤→🤖)
[ ] Brief, brand guide, voice guide, photos, business facts, reviews+GBP URL, GTM ID, domain plan

BUILD (🤖)
[ ] Page generated, brand-accurate, forms+honeypot+UTM, GTM events, SEO/a11y, reviews
[ ] Human copy review

ASSETS (👤→🤖)
[ ] Photos uploaded via GitHub (NOT chat paste) → optimized, named, wired, QA'd

CONTENT (🤖/👤)
[ ] Privacy policy + footer email + favicon + voice cleanup + CTA wording

REPO/DEPLOY (🤖→🌐)
[ ] Commit/push/draft PR, netlify.toml
[ ] Netlify linked to repo; production branch + publish dir correct; auto-deploy verified

DOMAIN (🌐/👤)
[ ] Custom domain added (external DNS); CNAME + ownership TXT at registrar; SSL green

FORMS (🌐/👤)
[ ] Form detection ENABLED + redeployed; both forms detected
[ ] Notifications set (test inbox → then real recipients); production test = 200 + Thank You + email received

LAUNCH (👤)
[ ] Google Ads final URL set; conversions firing; final live test; then enable spend
```

---

## Top 10 lessons (the stuff that cost us time)
1. **Chat-pasted images don't reach Claude Code** — upload to the GitHub repo instead.
2. **Drive originals are too big** to pull through the connector — GitHub upload sidesteps it.
3. **Browser cache** makes deploys look "broken" — verify via Deploy Preview URL or hard refresh.
4. **Netlify deploys the production branch** — set it to where the work lives, or it fails/serves stale.
5. **Netlify form detection is OFF by default** — enable it, then redeploy.
6. **Test forms on production**, not deploy-preview URLs.
7. **Form notifications ≠ deploy notifications** — different sections.
8. **Custom domains usually need a TXT** (ownership) in addition to the CNAME.
9. **Claude Code can't reach the internet** by default — that's why we use the Chrome agent for dashboards (or enable an egress allowlist).
10. **Netlify's native lead email isn't customizable** — add Zapier/Make/Function for custom formatting.
