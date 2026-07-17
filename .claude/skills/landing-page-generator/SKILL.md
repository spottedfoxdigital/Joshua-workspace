---
name: landing-page-generator
description: Generate a complete, conversion-optimized paid advertising landing page as a single self-contained HTML file. Use this skill whenever a client needs a landing page for a Google Ads, Meta, or other paid campaign — even if phrased casually ("build a landing page for [client]", "we need a campaign page", "let's make an LP for [campaign]", "paid ad page for [client]"). Always produces brand-accurate, W3C compliant, tracking-ready HTML ready for Netlify deployment. Never adds navigation, never exceeds 4 form fields, always bakes in the full tracking contract. Run end-to-end from intake through file delivery unless told otherwise.
---

# Paid Ad Landing Page Generator

Produces a single self-contained HTML landing page optimized for paid traffic conversion. Every page follows the same architecture: brand-accurate, fast-loading, W3C compliant, fully tracked, and deployment-ready.

---

## Core Rule: Intake Before Code

**Never write HTML before completing intake.** The sequence is always:

**Intake → Brand extraction → Template selection → Build → QA → Deliver**

Skipping intake produces generic pages that do not match the ad or the brand.

---

## Conversion Philosophy

Every decision on a landing page serves one goal: convert the visitor who just clicked an ad.

- **Message match is non-negotiable** - the LP headline must mirror the ad headline
- **One conversion path** - every CTA anchors to the same form, no exceptions
- **No escape routes** - no navigation, no internal links that leave the page
- **Trust before ask** - proof (reviews, guarantees, credentials) appears before the form when possible
- **Friction kills conversions** - 4 form fields maximum, inline success state, no redirects

---

## Required Inputs

Before writing any code, collect all of these. Extract from uploaded briefs or documents first, then ask only for what is missing.

| Input | Required | Notes |
|---|---|---|
| Client name + main website URL | Yes | Used for footer link and logo source |
| Campaign name | Yes | Used in tracking payload and handoff notes |
| Conversion goal | Yes | Form lead / phone call / Calendly booking |
| Ad headline / offer | Yes | Must match LP headline exactly - message match |
| Service or product being promoted | Yes | Drives copy and template choice |
| Target geography | Yes | City/region shown in headline and eyebrow |
| Phone number | Yes | Header, hero, footer, click-to-call tracking |
| Brand style guide or assets | Yes | Colors, fonts, logo - see Brand Extraction below |
| GTM Container ID | Yes | Or flag as "needs creation" |
| Proof points | Yes | Reviews (verbatim preferred), guarantees, certifications |
| Template type | Yes | See Template Selector below |
| Lead destination | Preferred | Google Sheets / Monday.com / email notification |
| Ad copy or landing page brief | Preferred | For message match and copy alignment |
| Photo assets | Optional | If none, use CSS placeholders with gallery labels |

---

## Template Selector

Choose the template that matches the campaign goal. When in doubt, default to Service Business.

| # | Template | Best For | Defining Characteristic |
|---|---|---|---|
| 1 | **Service Business** | Home services, contractors, tradespeople, retail | Products/services grid + 3-step process + gallery |
| 2 | **Professional Services** | Finance, legal, medical, consulting | Credentials/trust section replaces products grid; case study format for reviews |
| 3 | **Event / Promotion** | Time-limited offers, sales events, product launches | Countdown timer + offer details + what is included list |
| 4 | **Appointment Booking** | Any business driving directly to Calendly or a booking tool | Calendly embed replaces form; "what to expect" replaces process |
| 5 | **Lead Magnet** | Free guide, checklist, consultation, assessment offer | Download/access frame replaces product grid; preview section shows content value |

---

## Brand Extraction Protocol

Run this before writing a single line of CSS.

### If a style guide is uploaded
Extract directly:
1. Primary color (hex)
2. Secondary / accent color (hex)
3. Background color (hex) - default to white if not specified
4. Text color (hex)
5. Heading font name + weight
6. Body font name + weight
7. Logo file - request upload; always base64 encode inline (never hotlink)

### If no style guide
1. Ask for hex codes and font names directly
2. If they do not know fonts, web_fetch the main site and look for Google Fonts links or CSS font-family declarations
3. Confirm extracted values with the user before building

### Logo handling rule
Never use an external URL for logos - hosting servers block hotlinks and the logo will not render. Always:
1. Ask for the logo file (SVG preferred, PNG acceptable)
2. Base64 encode it: `base64.b64encode(open('logo.svg','rb').read()).decode()`
3. Embed as `src="data:image/svg+xml;base64,[encoded_string]"`
4. If two logo variants needed (e.g. icon in header, tagline in footer), encode both separately

---

## Hard Rules - Never Break These

These apply to every page regardless of client, template, or request:

- **Single HTML file** - no external CSS files, no external JS files
- **No navigation menu** - removes escape routes from paid traffic
- **No jQuery, no Bootstrap, no external JS libraries** - vanilla CSS and JS only
- **Maximum 4 form fields**: Name, Phone, Email, + one relevant qualifier (service/product interest)
- **Inline success state** - form shows confirmation message on submit; never redirect to a thank-you page
- **Every CTA button anchors to #estimate** - one destination on the page
- **noindex / nofollow meta tag** on every page - LP must not compete with or cannibalize the main site in search
- **GTM snippet always in `<head>` + noscript fallback in `<body>`**
- **Logo always base64 embedded** - never an external src URL
- **Honeypot spam field** on every form (hidden via CSS position absolute, not display:none)
- **Handoff notes comment block** at the bottom of every file before `</body>`

---

## Page Structure

### Standard Section Order (Service Business template - adapt for other templates)

```
1.  Sticky header        Logo + phone number + single CTA button
2.  Hero                 Eyebrow (geo) + H1 (message match) + subhead + trust chips + form card
3.  Trust strip          3 short trust statements on brand-color background
4.  Products / Services  3 cards with image, name, description, CTA
5.  Why Us               6 differentiator items with icon + title + description
6.  How It Works         3 numbered steps
7.  Reviews              Star rating badge + 2 review cards
8.  Gallery              6 image cells (replace placeholders with real photos before launch)
9.  Final CTA            Repeat of form on dark background
10. Footer               Logo + contact info + privacy policy link
```

### Sticky Header Spec
- Always visible, fixed to top
- Black or brand-dark background
- Logo left, phone center-right, CTA button far right
- Red border-bottom or brand accent line
- Becomes solid background on scroll (JS scroll listener)
- Phone number fires click_to_call dataLayer event

### Hero Spec
- Full-viewport-height section with real photo background
- Dark overlay (gradient, left-heavy so form stays legible)
- Left column: eyebrow + H1 + subhead + 4 trust chips + 2 CTA buttons (primary = red/brand, secondary = outline)
- Right column: form card (white background, brand accent top border, 4 fields + submit)
- Form card has: title ("Get Your Free Estimate"), sub ("No commitment required"), lock icon + privacy note, phone alternative link
- Both hero CTA buttons anchor to #estimate (the form card is #estimate)
- Fade-up animation on hero content, slight delay stagger

### Form Card Spec
```html
<form id="hero-form" onsubmit="handleSubmit(event,'hero-form')">
  <!-- Visible fields -->
  <input type="text" name="name" autocomplete="name" placeholder="Jane Smith" required>
  <input type="tel" name="phone" autocomplete="tel" placeholder="(509) 000-0000" required>
  <input type="email" name="email" autocomplete="email" placeholder="jane@email.com" required>
  <select name="service" required>
    <option value="">Select a service...</option>
    [relevant options for this client]
  </select>

  <!-- Honeypot - hidden via CSS, not display:none -->
  <input type="text" name="website" tabindex="-1" autocomplete="off"
    style="position:absolute;left:-9999px;opacity:0;height:0;width:0">

  <!-- UTM / tracking hidden fields (populated by JS on page load) -->
  <input type="hidden" name="utm_source" id="f_src">
  <input type="hidden" name="utm_medium" id="f_med">
  <input type="hidden" name="utm_campaign" id="f_cmp">
  <input type="hidden" name="utm_term" id="f_trm">
  <input type="hidden" name="utm_content" id="f_cnt">
  <input type="hidden" name="gclid" id="f_gclid">
  <input type="hidden" name="fbclid" id="f_fbclid">

  <button type="submit">Schedule My Free Estimate</button>
</form>
```

Form success state replaces the form HTML with a confirmation message. Never redirect.

---

## Tracking Contract

Every page implements this exact dataLayer taxonomy. Do not deviate.

### On Page Load
Capture UTM parameters and gclid/fbclid from the URL and push to dataLayer. Also populate hidden form fields.

```javascript
(function(){
  var p = new URLSearchParams(window.location.search);
  window._t = {
    utm_source:   p.get('utm_source')   || '',
    utm_medium:   p.get('utm_medium')   || '',
    utm_campaign: p.get('utm_campaign') || '',
    utm_term:     p.get('utm_term')     || '',
    utm_content:  p.get('utm_content')  || '',
    gclid:        p.get('gclid')        || '',
    fbclid:       p.get('fbclid')       || ''
  };
  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: 'page_params',
    page_type: 'landing_page',
    source: window._t.utm_source,
    medium: window._t.utm_medium,
    campaign: window._t.utm_campaign
  });
  // Populate hidden fields in hero form
  ['f_src','f_med','f_cmp','f_trm','f_cnt','f_gclid','f_fbclid'].forEach(function(id, i){
    var el = document.getElementById(id);
    if (el) el.value = Object.values(window._t)[i] || '';
  });
})();
```

### On Form Submit Success (generate_lead)
Fires exactly once, after confirmed POST success. Never on page load, never on redirect.

```javascript
window.dataLayer.push({
  event:     'generate_lead',
  page_type: 'landing_page',
  client_id: '{{CLIENT_ID}}',
  campaign:  '{{CAMPAIGN_NAME}}',
  form_id:   formId,
  value:     0,
  currency:  'USD'
});
```

### On Phone Link Click (click_to_call)
Fires on every `tel:` link on the page.

```javascript
window.dataLayer.push({
  event:     'click_to_call',
  page_type: 'landing_page',
  client_id: '{{CLIENT_ID}}',
  campaign:  '{{CAMPAIGN_NAME}}'
});
```

### On Booking Button Click (book_appointment)
Only on Appointment Booking template. Fires when user clicks the Calendly/booking link.

```javascript
window.dataLayer.push({
  event:     'book_appointment',
  page_type: 'landing_page',
  client_id: '{{CLIENT_ID}}',
  campaign:  '{{CAMPAIGN_NAME}}'
});
```

### page_type Rule
Every dataLayer push includes `page_type: 'landing_page'`. This allows GTM trigger filters to separate LP conversions from main site conversions in the same container - preventing inflation of site-wide conversion data.

### Lead Payload to Endpoint

```javascript
var payload = {};
new FormData(form).forEach(function(v,k){ payload[k] = v; });
Object.assign(payload, window._t || {});
payload.form_id  = formId;
payload.page_url = window.location.href;
payload.client_id = '{{CLIENT_ID}}';
payload.campaign  = '{{CAMPAIGN_NAME}}';
payload.timestamp = new Date().toISOString();
// Discard honeypot submissions
if (payload.website) return;

// TODO: replace with real endpoint
// fetch('{{LEAD_ENDPOINT}}', {
//   method: 'POST',
//   headers: {'Content-Type':'application/json'},
//   body: JSON.stringify(payload)
// }).then(function(r){ if(!r.ok) throw new Error(); }).then(ok).catch(err);
```

Payload fields captured: name, phone, email, service/qualifier, utm_source, utm_medium, utm_campaign, utm_term, utm_content, gclid, fbclid, form_id, page_url, client_id, campaign, timestamp.

---

## GTM Setup Reference

### Client already has a GTM container
Use the same container. Do not create a new one.

Add a `page_type` variable in GTM (Data Layer Variable → variable name: `page_type`). Add this as a filter condition on all conversion tags: page_type equals `landing_page`. This cleanly separates LP events from site events without a second container.

### Client has no GTM container
Create one container. Use it for both the main site and landing pages. Apply the same `page_type` filter approach from day one.

### GTM tags to configure per client (in their container)
- **GA4 Event tag** triggered by: `generate_lead`, `click_to_call`
- **Google Ads Conversion tag** triggered by: `generate_lead`
- **Meta Pixel custom event** triggered by: `generate_lead`, `click_to_call`
- All three tags use the `page_type = landing_page` filter

---

## Lead Destination Architecture

Phase 1 uses a two-layer approach so the destination can change without touching page code.

```
Form submit → Netlify Function (LEAD_ENDPOINT) → Supabase (permanent record)
                                               → Zapier trigger → client destination
```

### Client destination options (configure in Zapier)
- **Google Sheets** - simplest, most accessible for any client
- **Monday.com** - use for clients already on Monday
- **Email notification** - fallback or for low-volume clients
- **HubSpot / GoHighLevel** - for clients with existing CRM

The Supabase record is always written regardless of Zapier destination. It is the source of truth.

### Supabase leads table schema (reference)
```
id          uuid primary key default gen_random_uuid()
client_id   text not null
campaign    text not null
created_at  timestamptz default now()
form_id     text
name        text
phone       text
email       text
service     text
utm_source  text
utm_medium  text
utm_campaign text
utm_term    text
utm_content text
gclid       text
fbclid      text
page_url    text
raw         jsonb
```

---

## HTML Head Requirements

Every page must include all of these:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="[compelling description with geo and service]">
  <meta name="robots" content="noindex, nofollow">

  <!-- Open Graph -->
  <meta property="og:title" content="[H1 headline]">
  <meta property="og:description" content="[subhead copy]">
  <meta property="og:url" content="[campaign URL]">
  <meta property="og:type" content="website">

  <!-- JSON-LD LocalBusiness Schema -->
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "LocalBusiness",
    "name": "[client name]",
    "telephone": "[phone]",
    "areaServed": "[city/region]",
    "url": "[main website URL]"
  }
  </script>

  <!-- GTM -->
  <script>(function(w,d,s,l,i){...})(window,document,'script','dataLayer','{{GTM_ID}}');</script>

  <!-- Google Fonts with preconnect -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=[Font]:wght@...&display=swap" rel="stylesheet">

  <title>[Service] in [City], [State] | [Client Name]</title>
</head>
```

---

## CSS Architecture Rules

```css
/* Brand tokens at :root - always */
:root {
  --primary:   [brand primary hex];
  --primary-h: [hover state - darker];
  --dark:      [darkest color - for header/footer];
  --white:     #FFFFFF;
  --off-white: #F7F7F7;
  --text:      [body text color];
  --text-mid:  [secondary text];
  --text-light:[tertiary/placeholder text];
  --border:    rgba(0,0,0,0.12);
  --shadow:    0 4px 20px rgba(0,0,0,0.10);
  --shadow-lg: 0 12px 48px rgba(0,0,0,0.20);
  --radius:    [4px–12px depending on brand feel];
  --heading:   '[Font Name]', sans-serif;
  --body:      '[Font Name]', sans-serif;
}
```

- Mobile-first: base styles for mobile, `@media (min-width: 600px)` and `@media (min-width: 900px)` for larger
- `font-display: swap` appended to Google Fonts URL: `&display=swap`
- Images below the fold get `loading="lazy"`
- No inline styles except one-off positional overrides
- Compress CSS - remove comments and excess whitespace in final output

---

## Accessibility Requirements (WCAG 2.1 AA)

- All `<img>` tags have meaningful `alt` attributes
- All form `<input>` elements have associated `<label>` elements
- Color contrast: body text minimum 4.5:1, large text minimum 3:1
- Focus states visible on all interactive elements (do not remove outline without replacement)
- All `<button>` elements have descriptive text content
- `autocomplete` attributes on all form fields
- `aria-label` on icon-only buttons

---

## Performance Rules

- Target HTML file size: under 150KB (excluding base64 logo)
- No external JS libraries (no jQuery, no Alpine, no GSAP)
- GTM loads async via the standard snippet - do not modify
- Google Fonts loaded with `preconnect` hints and `display=swap`
- All images below the fold: `loading="lazy"`
- Images: WebP format, 800x600px for gallery, 1920x1080px for hero (or use CSS background-image)
- Avoid CSS `@import` - use `<link>` for fonts instead

---

## Handoff Notes Template

Always include this comment block at the very bottom of the file, just before `</body>`:

```html
<!--
HANDOFF NOTES - Remove before launch
========================================
CLIENT:    [name]
CAMPAIGN:  [name]
TEMPLATE:  [Service Business / Professional Services / Event / Appointment / Lead Magnet]
BUILT:     [date]
GTM ID:    [GTM-XXXXXX]

PLACEHOLDERS TO REPLACE BEFORE LAUNCH:
1. {{LEAD_ENDPOINT}}   Netlify function URL (or Supabase REST endpoint)
2. {{GTM_ID}}          Already baked in if GTM ID was provided
3. Review count        Search "update with live count" - replace with real number
4. Gallery images      6 placeholder cells - replace with real photos
                       Run web-photo-optimizer skill: 800x600 WebP, lazy loading

GTM TAGS TO CONFIGURE (container: [GTM-XXXXXX]):
- GA4 Event:            generate_lead + click_to_call  (filter: page_type = landing_page)
- Google Ads Conversion: generate_lead                 (filter: page_type = landing_page)
- Meta Pixel:           generate_lead + click_to_call  (filter: page_type = landing_page)

LEAD DESTINATION:
- Supabase table: leads (permanent record, always written)
- Zapier → [Google Sheets / Monday.com / email notification]

DEPLOY:
- Netlify: drag-drop index.html or CLI deploy
- Recommended: one Netlify site per campaign
- Suggested custom domain: [campaign-slug].[clientdomain].com

INTENTIONAL DEVIATIONS FROM BRIEF:
[List any here - e.g. "Navigation removed - paid LP standard"]

POST-LAUNCH:
- Confirm generate_lead firing in GTM Preview before scaling ad spend
- Verify Supabase row written on test submission
- Confirm Zapier trigger delivers to [destination]
========================================
-->
```

---

## QA Checklist Before File Delivery

Run through every item. Do not deliver until all pass.

**Code Quality**
- [ ] W3C HTML validation passes with zero errors (https://validator.w3.org)
- [ ] No external JS libraries referenced
- [ ] noindex / nofollow meta tag present
- [ ] JSON-LD schema present and valid
- [ ] OG tags present

**Conversion**
- [ ] No navigation menu visible
- [ ] Both form instances work (hero + bottom CTA)
- [ ] Form success state shows on submit (no redirect)
- [ ] Honeypot field present (position absolute, not display:none)
- [ ] All CTA buttons anchor to #estimate
- [ ] Phone number present in header, hero, and footer

**Tracking**
- [ ] GTM container ID baked in (or clearly marked as placeholder)
- [ ] UTM / gclid / fbclid populated in hidden fields on page load
- [ ] generate_lead fires exactly once on form success
- [ ] click_to_call fires on every tel: link
- [ ] page_type: 'landing_page' present in every dataLayer push
- [ ] Lead payload includes all required fields
- [ ] Honeypot check in submit handler (discard if payload.website is truthy)

**Brand**
- [ ] Logo renders (base64 embedded, not external URL)
- [ ] Brand colors match style guide
- [ ] Brand fonts loaded correctly

**Responsive**
- [ ] Layout correct at 375px (mobile)
- [ ] Layout correct at 768px (tablet)
- [ ] Layout correct at 1280px (desktop)
- [ ] Form accessible on mobile without horizontal scroll

**Handoff**
- [ ] Handoff notes comment block present at bottom of file
- [ ] File size noted (mention in response to user)
- [ ] All placeholders clearly marked with {{PLACEHOLDER}} syntax

---

## Delivery

Save as `[ClientSlug]_[CampaignSlug]_Landing.html` in `/mnt/user-data/outputs/` and use `present_files` to share.

Example: `BlindGuy_Wenatchee_Landing.html`

State the file size and list the three most important pre-launch action items from the handoff notes when delivering.
