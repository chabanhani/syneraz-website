# Syneraz Corporate Website — Developer Handoff

**Prepared by:** Brand Manager (`agent-brand`) · **Date:** 2026-06-27
**Design:** "Impact" direction (dark navy/teal corporate theme)
**Status:** English production build ready. Arabic/RTL build to follow (this spec includes its requirements). Final marketing copy and a few open items below before launch.

This folder **is** the production English site. It mirrors the proven `securityrelief.com` stack: static HTML/CSS/JS, no framework, no build step, served by Caddy on the DigitalOcean droplet.

---

## 1. What's in this folder

```
2026-06-27-syneraz-corporate-PRODUCTION/
├── index.html                ← English page (dir="ltr" lang="en")
├── assets/
│   ├── css/styles.css         ← all styles (one file, serves both EN + AR via logical properties)
│   ├── js/site.js             ← all behaviour (header scroll, scroll-reveal, mobile menu, form)
│   └── img/                    ← logos, brush, symbol, favicons, OG image
├── en/                         ← (empty placeholder — see Language Routing §3)
├── robots.txt
├── sitemap.xml
├── Caddyfile.snippet           ← drop-in Caddy block (security headers, www→apex, caching)
└── README-developer-handoff.md ← this file
```

> The CSS already uses logical properties (`inset-inline`, `margin-inline`, `padding-inline`, `border-inline-*`) **throughout**, specifically so the **same stylesheet serves the Arabic RTL build** with no fork. Do not duplicate CSS per language.

---

## 2. Stack & deploy (mirror securityrelief.com)

| Area | Detail |
|------|--------|
| **Type** | Static HTML + CSS + vanilla JS. No CMS, no framework, no build step. |
| **Hosting** | Same DigitalOcean droplet + Caddy static serving as SecurityRelief. |
| **Deploy** | `git pull` on the server. No rebuild, no downtime. Follow the `sr-website-deploy` runbook pattern. |
| **Domain** | `syneraz.com` (apex) + `www.syneraz.com` → apex redirect. Confirm DNS points to the droplet. |
| **TLS** | Caddy auto-TLS (Let's Encrypt). |

`Caddyfile.snippet` is ready to paste; adjust the `root` path to the pulled repo location.

---

## 3. Language routing (EN now, AR to follow)

Final structure:
- `/`     → **Arabic** (canonical, primary), `dir="rtl" lang="ar"`
- `/en/`  → **English**, `dir="ltr" lang="en"`

**The Arabic page is not built yet.** Two valid interim approaches — pick one with the Brand Manager:
1. **Launch EN-only first:** serve this `index.html` at `/` temporarily; add `/en/` and the Arabic `/` when the AR build lands. Update `sitemap.xml` + `hreflang` at that point.
2. **Hold launch until AR is ready:** the Brand Manager delivers the Arabic copy + RTL page, then both ship together at `/` (AR) and `/en/` (EN).

The `hreflang` tags, `sitemap.xml`, and Caddy comments already anticipate the AR/EN split.

### Arabic build requirements (when built)
- `dir="rtl" lang="ar"` on `<html>`. The shared `styles.css` already handles RTL via logical properties — **reuse it as-is**.
- **Fonts:** Arabic uses **Tajawal** (Google Fonts, weights 400/500/700). Swap the Quicksand/Inter Latin stack for Tajawal on Arabic headings + body. (Latin display can stay Quicksand for any Latin words.)
- Mirror every section; the language pill swaps `/` ⇄ `/en/`.
- Arabic is the **canonical** language for SEO. Final Arabic copy is authored by the Brand Manager — **do not machine-translate.**

---

## 4. Sections (final structure — approved)

In order: **Header → Hero → What we do → Why Syneraz → Products & Services → How we work → Latest from Syneraz → Contact → Footer.**

- **Hero:** signature line "COMMITTED. DELIVERED.", headline "Hard challenges, solved simply.", single CTA "See our first solution" → `#product`. Large static Syneraz monogram bleeds off the right edge.
- **What we do:** 3 principles (Simple / Practical / Intelligent), line-divided columns.
- **Why Syneraz:** 3 benefit cards (A solution not a service bill / Clarity from day one / Built for your reality).
- **Products & Services:** 3 cards — SecurityRelief (live, white feature card, links to securityrelief.com + assessment), "Agents, made simple" (next focus), "More practical solutions" (exploring).
- **How we work:** glass statement card + 3 process steps (Understand / Build the solution / Make it simple to start).
- **Latest from Syneraz:** 3 news cards (SecurityRelief launch / Cisco & Microsoft partner / Live on LinkedIn) + a LinkedIn follow CTA band. **These cards are content slots** — swap in real posts/links over time.
- **Contact:** form (Name / Organization / Email / Request type / Message) + email, Jeddah location, LinkedIn.

---

## 5. ⚠️ The contact form — you must wire it

The form in `assets/js/site.js` is **front-end only** — on submit it just swaps in a thank-you message. It does **not** send anywhere.

**Make it send (your choice of backend — pick one):**
- POST to an **n8n webhook + email relay** (reuse Syneraz infra, like SecurityRelief — confirm endpoint with `agent-tech`), **or**
- a hosted form service (Formspree, or the Resend agent-email layer), **or**
- any equivalent that meets the requirements below.

**Requirements regardless of backend:**
- Validate server-side (don't trust the client).
- **Keep the honeypot** field `name="website"` (hidden; if filled, drop as bot).
- Add a basic rate limit.
- Deliver to a **monitored inbox** (`info@syneraz.com` or wherever Hani routes it).
- On success, keep the existing inline confirmation UX.
- Wire the **"Request type"** dropdown value (General / Partnership / Work with us) into the delivered payload.

Everything else (scroll-reveal, sticky header, mobile menu, year stamp) works as-is.

---

## 6. Assets

**Included, final-for-now (raster PNG):**
- `syneraz-logo-reversed.png` — header + footer (white wordmark + teal monogram on dark).
- `hero-monogram.png` — hero visual + the small symbol used before each section eyebrow (white+teal, for dark backgrounds).
- `syneraz-symbol-color.png` — navy+teal symbol (favicon/OG source; used on the white SecurityRelief card eyebrow).
- `sr-endorsed.png` — "SecurityRelief by Syneraz" endorsed lockup (the product's own Royal/Sky identity — correct).
- `sr-icon.png` — SecurityRelief shield (currently unused in this layout; safe to keep).
- `favicon-32.png`, `apple-touch-icon.png`, `icon-192.png`, `og-image.png` — generated.
- `brush-teal.png` / `brush-white.png` — extracted logo brushstroke (currently unused after the design moved off brush accents; safe to leave or remove).

**To commission (does NOT block launch; upgrade when ready):**
- **Vector masters (.svg)** for all Syneraz logos — current files are raster. Swap PNG→SVG when available for crisp scaling, especially the large hero monogram.

---

## 7. SEO / performance / accessibility — already in place

- Per-language `<title>` + meta description, canonical, `hreflang` (ar/en/x-default).
- Open Graph + Twitter card tags; `og-image.png` (1200×630).
- JSON-LD `Organization` schema (name, Jeddah SA, logo, email, LinkedIn `sameAs`).
- `robots.txt` + `sitemap.xml`.
- Fonts via Google Fonts with `display=swap`; `preconnect` hints.
- Semantic landmarks (`<header> <main>`-equivalent sections `<nav> <footer>`), labelled form fields, `aria-hidden` on decorative imagery, focus-visible styles, `prefers-reduced-motion` honored (all animation disabled).

**Run before launch:** Lighthouse on mobile (target ≥ 90 all categories) and a real-device RTL check once the Arabic page exists.

---

## 8. Visual polish to apply (Brand Manager's review notes)

Minor refinements flagged in review — apply during build:

1. **Section background tone:** "Products & Services" ends on the `--bg2` tone and "How we work" also uses `--bg2`, so the two adjacent sections can read flat where they meet. Nudge one section's background a step lighter/darker to restore separation.
2. **Glass card blur:** the "How we work" statement card uses `backdrop-filter: blur()`, which only shows when there's content behind it to blur. Against the flat section it reads mostly as a tinted card — acceptable, but if you add any subtle background texture/pattern behind it, the glass effect will read properly.
3. **Mobile hero monogram:** drops to ~18% opacity as a background watermark on small screens. Verify on a real phone that it doesn't reduce headline legibility; dial opacity down further if needed.

---

## 9. Open items to confirm before launch

1. **Final marketing copy (EN + AR).** Current EN copy is brand-approved working copy. Final copy (and all Arabic copy) is owned by the Brand Manager / content lane. Structure and CSS hooks won't change.
2. **Arabic page** — to be built (see §3). Brand Manager delivers AR copy + RTL page.
3. **Cisco & Microsoft partnership claim** — the "Latest" card states Syneraz is an official member of both partner programs. **Do not publish until both are confirmed active.** Text-only (no vendor logos — trademark). If only one is finalized at launch, reword to the confirmed one.
4. **Contact endpoint** — wire per §5 (confirm with `agent-tech`).
5. **Analytics** — none wired. Add privacy-respecting analytics (Plausible or GA4 — confirm with Hani). Track the hero CTA and outbound clicks to SecurityRelief/assessment.
6. **Domain/DNS** — confirm `syneraz.com` registered and pointed at the droplet.
7. **Privacy note** — short PDPL-aware note for the contact form + analytics once analytics is chosen.
8. **Mandatory pre-launch brand QA** via the Brand Manager (`marketing:brand-review`) once final copy is in.

---

## 10. Brand guardrails (do not regress)

- **Corporate palette = Navy `#003366` + Teal `#33CCCC`.** SecurityRelief's Royal/Sky (`#20409A` / `#4694C8`) appears **only** inside the SecurityRelief card. Never mix.
- **No fabricated equity.** No client logos, trust badges, testimonials, invented stats, or awards. The company is pre-revenue. Keep the "Latest" news cards factual.
- **Endorsed model:** "SecurityRelief by Syneraz", never "Syneraz SecurityRelief." Syneraz is a practical-solutions company, not a cybersecurity company.
- **NCA** referenced with genuine respect.
- **No em dashes (—) in body copy** — this was a deliberate editorial rule across the site; keep it when adding/editing copy.

---

*Brand foundation: `syneraz/_brand/corporate/{positioning,messaging-architecture,visual-identity}.md`. Architecture: `syneraz/_brand/brand-architecture.md`. Final pre-launch brand QA is mandatory.*
