# The Guest Experience Summit — Developer Handoff Package

Static HTML/CSS/JS prototype of the full website, built as a 1:1 visual and content
reference for implementation in Next.js + Headless CMS. Open `index.html` directly
in a browser — no build step required.

## Structure

```
The-Guest-Experience-Summit/
├── index.html                  Home
├── agenda.html                 Agenda (day tabs, track filters, session timeline)
├── speakers.html                Speakers listing (filterable grid)
├── speaker-detail.html          Speaker profile template
├── partners.html                Partners & sponsorship (packages, lead form)
├── insights.html                Insights listing (articles, reports, video, podcast)
├── article.html                 Single article/report template
├── networking.html              Networking & experiences
├── about.html                   About / mission / team / history
├── contact.html                 Contact (department routing, FAQ, form)
├── request-invitation.html      Qualification application form
│
├── assets/
│   ├── css/
│   │   └── shared.css           Single source of truth: tokens, components, layout
│   ├── js/
│   │   └── main.js              Filters, tabs, accordions, form handling
│   ├── images/                  (empty — pages currently hotlink Unsplash placeholders)
│   └── icons/                   (empty — icons are inline SVG in markup)
│
└── docs/
    ├── information-architecture.md   Sitemap, content relationships, routing strategy
    ├── cms-schema.md                 CMS-managed collections and field definitions
    └── database-schema.md            Transactional tables (forms, applications)
```

## Design system

All 11 pages reference the same `assets/css/shared.css` — color tokens, type scale,
spacing, button/card/badge/form components, nav, and footer are defined once.
Home's hero is bespoke (flagship entry point); every interior page reuses the shared
`.page-hero` pattern. Do not fork CSS per page.

## Known placeholders (review before launch)

- **Images**: hotlinked from Unsplash for prototyping. Replace with licensed photography
  and real headshots before production; route through `assets/images/`.
- **Logos**: partner/brand logos are currently styled text, not real marks.
- **Copy**: bios, the featured report, sponsorship pricing, team names, and FAQ answers
  are realistic drafted content, not client-supplied — verify every claim and price
  point before publishing.
- **Forms**: `main.js` intercepts submit events for demo purposes (button shows
  "Submitted ✓"). Wire to real API routes per `docs/database-schema.md` before launch.

## Next.js migration notes

See `docs/information-architecture.md` §6 for routing/rendering strategy (SSG+ISR for
content pages, SSR for the invitation status page, API routes for form writes).
`docs/cms-schema.md` and `docs/database-schema.md` give the full field-level schema
for the headless CMS and the application database respectively.
