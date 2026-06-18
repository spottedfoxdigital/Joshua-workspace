# Landing Page Starter Template

A clone-and-fill starter derived from the Blind Guy of Wenatchee build. Copy this folder
for each new client, replace the `{{PLACEHOLDERS}}`, drop in assets, and deploy per the
[Deployment Playbook](../docs/DEPLOYMENT-WORKFLOW.md).

> **Vertical:** built as a **local service business** page (hero + 3 services + why-us +
> 3-step process + reviews + gallery + dual lead forms). The *structure* is reusable for any
> service business; the example *copy* is window-treatments and should be rewritten per client.

---

## How to use (per new client)

1. **Copy** this folder to `landing-pages/<client-city>/`.
2. **Find & replace** every `{{PLACEHOLDER}}` (table below) in `index.html` and `privacy.html`.
   Tip: Claude Code can do this in one pass if you give it the values.
3. **Add assets:** logo (the `{{LOGO_BASE64}}` spots), favicons, and photos in `images/`
   (see `images/README.md` for the expected filenames).
4. **Rewrite the example body copy** per client/industry (checklist below).
5. **Deploy** following the playbook (own Netlify site, custom domain, enable form detection).

---

## Placeholder reference

### Business identity
| Placeholder | What it is | Blind Guy example |
|---|---|---|
| `{{BUSINESS_NAME}}` | Short brand name | The Blind Guy |
| `{{BUSINESS_NAME_LOCAL}}` | Full/local brand name | The Blind Guy of Wenatchee |
| `{{CITY}}` | Target city | Wenatchee |
| `{{STATE}}` | State abbreviation | WA |
| `{{REGION}}` | Broader service area | Wenatchee Valley |
| `{{SERVICE_HEADLINE}}` | Headline service phrase | Custom Window Treatments |

### Contact
| Placeholder | What it is | Example |
|---|---|---|
| `{{PHONE_DISPLAY}}` | Human-readable phone | (509) 980-1402 |
| `{{PHONE_E164}}` | `tel:` link format | +15099801402 |
| `{{PHONE_SCHEMA}}` | JSON-LD telephone | +1-509-980-1402 |
| `{{CONTACT_EMAIL}}` | Public contact email | tristaff@blindguy.com |

### URLs
| Placeholder | What it is | Example |
|---|---|---|
| `{{SITE_DOMAIN}}` | This LP's domain (no protocol) | wenatchee.blindguyoftricities.com |
| `{{MAIN_DOMAIN}}` | Client's main website domain | blindguyoftricities.com |
| `{{GBP_REVIEW_URL}}` | Google Business Profile reviews link | https://share.google/… |

### Tracking
| Placeholder | What it is | Example |
|---|---|---|
| `{{GTM_ID}}` | Google Tag Manager container | GTM-KRK5NSR |
| `{{CLIENT_ID}}` | dataLayer client identifier | blindguy_wenatchee |
| `{{CAMPAIGN_ID}}` | dataLayer campaign identifier | wenatchee_window_treatments |

### Brand styling
| Placeholder | What it is | Example |
|---|---|---|
| `{{BRAND_PRIMARY}}` | Primary brand hex | #FE0006 |
| `{{BRAND_PRIMARY_DARK}}` | Darker hover shade | #C70005 |
| `{{BRAND_PRIMARY_RGB}}` | Primary as `r,g,b` (for rgba shadows) | 254,0,6 |
| `{{HEADING_FONT}}` | Heading font family | Montserrat |
| `{{BODY_FONT}}` | Body font family | Ubuntu |
| `{{LOGO_BASE64}}` | Base64 logo data URI (header + footer) | data:image/png;base64,… |

> **Fonts note:** `{{HEADING_FONT}}`/`{{BODY_FONT}}` also appear in the Google Fonts `<link>`
> URL. After replacing the names, double-check the URL's `wght@` weights match what your fonts
> offer.
> **Dark neutral:** `#0A0A0A` (near-black) is left hard-coded as a neutral; change it only if
> the brand needs a different dark.

### Reviews (real, verbatim — replace with the client's actual Google reviews)
| Placeholder | What it is |
|---|---|
| `{{RATING}}` | Star rating shown in the badge (e.g. 5.0) |
| `{{REVIEW_1_TEXT}}` / `{{REVIEW_2_TEXT}}` / `{{REVIEW_3_TEXT}}` | Verbatim review text |
| `{{REVIEW_1_NAME}}` / `_2_` / `_3_` | Reviewer display name |
| `{{REVIEW_1_INITIALS}}` / `_2_` / `_3_` | Avatar initials (e.g. SW) |

### Privacy page
| Placeholder | What it is | Example |
|---|---|---|
| `{{EFFECTIVE_DATE}}` | Privacy policy effective date | June 17, 2026 |

---

## Body copy to rewrite per client (NOT tokenized — edit directly)

These sections carry the window-treatment example copy. Rewrite for the client's industry,
following the client's voice guide (no em-dashes, contractions, benefit-first, local refs):

- [ ] **Hero subhead** (`.hero-sub`)
- [ ] **Hero trust chips** (4 `.chip` items)
- [ ] **Lead-form product dropdown** options (`<select name="service">`) — match the services
- [ ] **Services section** — the 3 `.prod-card` headings + descriptions + the section `h2`/lead
- [ ] **Why-Us section** — 6 `.why-item` headings + descriptions
- [ ] **Process section** — 3 `.step` titles + descriptions
- [ ] **Gallery captions** — 6 `.gal-cell` `<span>` labels (and add matching photos)
- [ ] **Final CTA** copy (`.final-copy`)
- [ ] **Footer** columns (services list, hours, etc.)

> The form field **names** (`estimate-hero`, `estimate-contact`) can stay as-is or be renamed
> per client — just keep each form's `name` and its hidden `form-name` value identical.

---

## Assets checklist
- [ ] Logo → base64-encode and paste into both `{{LOGO_BASE64}}` spots (header + footer), or
      swap those `<img src>` to a hosted/relative logo file.
- [ ] Favicons → generate from the client's mark (`.ico`, 16/32 PNG, `apple-touch-icon.png`)
      and place in the client folder (the `<head>` already links them).
- [ ] Photos → add to `images/` using the filenames in `images/README.md`.
