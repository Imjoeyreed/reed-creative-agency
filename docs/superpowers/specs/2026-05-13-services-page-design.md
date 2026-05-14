# Services Page — Design Spec

**Date:** 2026-05-13
**Status:** Approved (design); pending implementation plan

## Goal

Move the services content off the homepage into its own dedicated `services.html` page, and add two new service offerings: **AI Product Content** and **Web Design & SEO**. No pricing anywhere — every service has a contact CTA only.

## Context

- Source of truth for the homepage is `index.html` (live, committed). `index-v2.html` is an untracked WIP redesign and is **out of scope** — leave it untouched.
- The site is hosted on Netlify, static files from repo root, auto-deploy on push to `main`.
- Standalone pages (`about.html`, `brands.html`, `celebrities.html`) use a lighter Cormorant/Montserrat aesthetic. The homepage's services section uses a dark Barlow Condensed / Playfair aesthetic.
- **Decision:** `services.html` keeps the dark services aesthetic (Approach 1 — lift the existing section as-is). The dark rows are the strongest design element on the site; reproducing them in the light aesthetic was rejected.

## Scope

### New file: `services.html`

A standalone dark page, structured top to bottom:

1. **Nav** — the full hamburger nav + fullscreen overlay menu lifted from `index.html` (not the simple "← Back" nav used by other standalone pages). Menu links that point to homepage anchors (`#china`, `#our-clients`) must become `index.html#china`, `index.html#our-clients` so they work from this page. The "Services" menu entry points to `services.html` (current page).
2. **Page header** — reuses the existing services section header: label "Expertise" + title "What we *do.*" ("do." in Playfair italic gold). Dark background.
3. **Six service rows** — the exact dark alternating photo/body row layout from `index.html`, styling unchanged. Each row gets an `id` anchor for deep-linking:
   - `#service-01` — Mega KOL & Celebrity Campaigns
   - `#service-02` — Creator Management
   - `#service-03` — Chinese Market (retains "Coming Soon" badge)
   - `#service-04` — Brand Activations
   - `#service-05` — **AI Product Content** (new)
   - `#service-06` — **Web Design & SEO** (new)
4. **Footer** — same footer as the other pages, dark to match.

Every row keeps its CTA link to `contact.html`. No prices.

### New services content (working copy — refine during implementation)

**05 — AI Product Content**
- Category label: "AI Production"
- Heading: "AI Product Content" ("Content" in Playfair italic gold)
- Description: Studio-grade photos and videos of products, generated with AI — no shoots, no sets, no waiting. Scroll-stopping creative at the speed of the client's roadmap.
- CTA: "Get Product Content" → `contact.html`
- Image: placeholder panel on ship; wired to swap to `pictures/ai product content.JPG` when provided.

**06 — Web Design & SEO**
- Category label: "Digital"
- Heading: "Web Design & SEO" ("& SEO" in Playfair italic gold)
- Description: Conversion-built websites plus the search visibility to fill them. Design, build, and rank — so campaign traffic lands somewhere that sells.
- CTA: "Build My Site" → `contact.html`
- Image: placeholder panel on ship; wired to swap to `pictures/web design seo.JPG` when provided.

Both new rows use the same scroll-reveal animation class as the existing four.

### Edited file: `index.html`

- Replace the full `#services` block (approx. lines 1205–1311) with a compact **teaser**:
  - Section header identical to the page header above (label "Expertise" + "What we *do.*").
  - A numbered list (01–06) of all six service **names only** — no photos, no descriptions.
  - Each list row deep-links to its service: `services.html#service-NN`.
  - Row 03 carries the "Coming Soon" badge; rows 05/06 carry a small gold "New" marker.
  - A "View All Services →" button linking to `services.html`.
  - The section retains `id="services"`.
- Hamburger menu "Services" link: `#services` → `services.html`.

### Edited files: other pages

- `about.html`, `brands.html`, `celebrities.html`, `contact.html` — audit each nav/menu and footer; any "Services" link changes to `services.html`.
- `sitemap.xml` — add an entry for `services.html`.

### Internationalization

The site has an EN/中文 toggle using the `data-lang` / `data-en` / `data-zh` pattern. All new content — the two new service rows (names, category labels, descriptions, CTAs) and the homepage teaser block — must include Chinese translations following the existing pattern.

## Out of scope

- `index-v2.html` (untracked WIP redesign) — untouched.
- Existing four services' content and photos — unchanged.
- The `contact.html` form — unchanged.
- Actual generation of the AI product images — the page ships with placeholder panels; images are a follow-up the user provides.
- Any pricing or package-tier UI — explicitly excluded.

## Success criteria

- `services.html` exists, renders all six service rows in the dark aesthetic with working hamburger nav and footer.
- Homepage shows the compact teaser; "View All Services" and each list row navigate correctly (deep-links land on the right row).
- Every "Services" link site-wide points to `services.html`; no remaining `#services` nav links except the homepage's own retained section id.
- New rows 05/06 display cleanly with placeholder panels and are pre-wired for their image filenames.
- EN/中文 toggle works for all new content.
- `sitemap.xml` includes `services.html`.
