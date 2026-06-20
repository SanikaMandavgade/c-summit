# The Guest Experience Summit — Developer Handoff Package

Static HTML/CSS/JS website, fully self-contained and GitHub Pages-ready. Open
`index.html` directly in a browser, or push this folder's contents to a GitHub repo
and enable Pages — no build step, no external dependencies besides Google Fonts.

## Structure

```
The-Guest-Experience-Summit/
├── .nojekyll                    Disables Jekyll processing on GitHub Pages
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
│   ├── images/                  27 local SVG placeholders (zero external image deps)
│   └── icons/                   (icons are inline SVG in markup — folder reserved)
│
└── docs/
    ├── information-architecture.md   Sitemap, content relationships, routing strategy
    ├── cms-schema.md                 CMS-managed collections and field definitions
    └── database-schema.md            Transactional tables (forms, applications)
```

## GitHub Pages deployment

1. Push the **contents** of this folder to the root of your repo (or to `/docs` if
   you prefer that Pages source option) — do not nest it inside another folder.
2. In repo Settings → Pages, set the source branch/folder and save.
3. `.nojekyll` is already included, which stops GitHub's Jekyll build step from
   altering or ignoring the `assets/` directory.
4. All internal links are relative (`assets/css/shared.css`, `agenda.html`, etc.),
   not absolute — so the site works both at a root domain and at a project subpath
   (`username.github.io/repo-name/`).

## Design system

All 11 pages reference the same `assets/css/shared.css` — color tokens, type scale,
spacing, button/card/badge/form components, nav, and footer are defined once. Each
page also carries a small `<style>` block for page-specific components (e.g. the
agenda day-tabs, the speaker grid) that aren't reused elsewhere — this is intentional
and does not affect the shared system. Responsive breakpoints: 1100px (tablet) and
640px (mobile), defined in `shared.css` and supplemented per-page where needed.

## Known placeholders (review before launch)

- **Images**: 27 local SVG placeholders in `assets/images/`, each labeled with its
  intended subject. Zero external image hosts are used. Replace with licensed
  photography/headshots before production.
- **Logos**: partner/brand logos are currently styled text, not real marks.
- **Copy**: bios, the featured report, sponsorship pricing, team names, and FAQ
  answers are realistic drafted content, not client-supplied — verify every claim
  and price point before publishing.
- **Forms**: `main.js` intercepts submit events for demo purposes (button shows
  "Submitted ✓"). Wire to real API routes per `docs/database-schema.md` before launch.

## Next.js migration notes

See `docs/information-architecture.md` §6 for routing/rendering strategy (SSG+ISR for
content pages, SSR for the invitation status page, API routes for form writes).
`docs/cms-schema.md` and `docs/database-schema.md` give the full field-level schema
for the headless CMS and the application database respectively.

