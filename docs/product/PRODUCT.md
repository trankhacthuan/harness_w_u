# Product Brief

Canonical description of the product this repo builds. Design and
implementation both start from this file (`docs/DESIGN.md`,
`docs/decisions/0010-single-repo-design-ownership.md`). For a restaurant site,
fill `docs/templates/brief-restaurant.md` and mirror the facts here.

Keep this file current. Content gaps are placeholders listed in
`docs/product/MISSING.md` (`docs/CONTENT_RULES.md`).

> Status: TEMPLATE — fill in when the first real product is chosen.

---

## 1. One-liner

<What the product is, in one sentence.>

## 2. Problem and goal

- Problem it solves:
- Primary goal / success metric:

## 3. Audience

- Primary users (who, context, device mix):
- Secondary users:
- Technical comfort level (low / medium / high):
- Locale / language(s):

## 4. Product type and scale

- Type: marketing site / web app / dashboard / docs / storefront / other
- Rough page count:
- Public vs authenticated areas:
- Content update frequency (static / occasional / live):

## 5. Core user journeys

1. <Journey name> — user does X to achieve Y.
2. ...

## 6. Key screens / routes

| Route | Purpose | Main content | Async? (needs loading/empty/error) |
| --- | --- | --- | --- |
| `/` | | | |
| | | | |

## 7. Content and data types

- Content objects (e.g. article, product, profile, event):
- Media (images / video / none):
- Forms (which, and what they collect):
- Third-party embeds (maps, payments, analytics):

## 8. Voice and tone

- Personality in 3–5 adjectives (e.g. calm, precise, friendly, bold, minimal):
- What it should NOT feel like:
- Reference sites / products the user likes (learn structure, do not copy):

## 9. Brand and style direction

- Existing brand assets? (logo, colors, fonts) — link or "none":
- Density: airy / balanced / compact
- Mood: light / dark / either
- Motion: none / subtle / expressive
- Imagery style: photographic / illustrated / abstract / none

## 10. Hard constraints

- Accessibility: WCAG 2.1 AA baseline (non-negotiable)
- Responsive: works 320px → wide desktop
- Performance budget: <e.g. LCP < 2.5s on 4G, JS < 200KB gzip>
- SEO: <needed? which pages must be indexable / have metadata>
- Browser support: <e.g. last 2 versions evergreen>
- Legal / compliance: <cookie consent, GDPR, none>

## 11. Explicitly out of scope

- Third-party feature APIs (social login, email/SMS delivery, maps APIs,
  payments) unless a story explicitly adds them.
- Authentication and an admin UI unless a story explicitly adds them.
- <Other non-goals.>

## 12. Open questions

- <Unresolved product decisions.>
