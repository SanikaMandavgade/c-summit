# The Guest Experience Summit — Information Architecture

## 1. Sitemap

```
/                              Home
/agenda                        Agenda (all days)
/agenda/[day-slug]             Day detail (anchor-scrolled, same template)
/speakers                      Speakers listing
/speakers/[speaker-slug]       Speaker detail page
/partners                      Partners & sponsorship
/partners/[partner-slug]       Partner detail page (logo wall click-through)
/insights                      Insights listing (articles, reports, video, podcast)
/insights/[article-slug]       Single article/report page
/networking                    Networking & experiences
/about                         About / mission / team / history
/contact                       Contact
/request-invitation            Qualification + application form
/request-invitation/status     Post-submit status page (pending/approved/waitlist)
```

## 2. Global Navigation

**Primary nav (header):** Home · Agenda · Speakers · Partners · Insights · Networking · About
**Utility nav (header right):** Request Invitation (primary button)
**Footer nav columns:** Event (Agenda, Speakers, Networking, Insights) · Attend (Request Invitation, Eligibility, FAQ) · Partner (Sponsorship, Partners, Case Studies) · Contact (Location, Date, Get in Touch)

Contact is reachable from footer + dedicated CTA banners, not primary nav — keeps top-level nav at 6 items (the proven threshold for executive-audience scanability).

## 3. Page → Primary Job → Primary Conversion

| Page | Primary job | Primary CTA |
|---|---|---|
| Home | Establish prestige + exclusivity, route to all paths | Request Invitation |
| Agenda | Prove substance/quality of content | Download Agenda PDF |
| Speakers | Prove caliber of attendee/speaker network | View Speaker Profile |
| Speaker Detail | Build credibility of one leader, cross-sell session | Add to My Agenda / View Session |
| Partners | Convert sponsors, communicate packages | Request Sponsorship Deck |
| Insights | Demonstrate thought leadership, build SEO/organic | Subscribe to Newsletter |
| Article Detail | Deliver value, nurture toward invitation | Related articles / Request Invitation |
| Networking | Sell the "exclusive access" emotional hook | Reserve Roundtable Seat |
| About | Build institutional trust/legitimacy | Request Invitation |
| Contact | Route inquiries to correct department | Submit Inquiry |
| Request Invitation | Qualify + capture applicant data | Submit Application |

## 4. Content Model Relationships (high level)

```
Event (1) ──< EventDay (many) ──< Session (many) >── Speaker (many, M:M via SessionSpeaker)
Session >── Track (many:1)
Session >── Category (many:1)
Speaker >── Company (many:1)
Speaker >── SpeakerCategory (M:M)
Partner >── PartnerTier (many:1)
Partner >── PartnerCategory (M:M)
Article >── Author (many:1)
Article >── ArticleCategory (many:1)
Article >── Tag (M:M)
InvitationApplication >── Company (many:1, free text or linked)
InvitationApplication >── ApprovalStatus (enum, workflow-driven)
RoundtableSession >── Track (many:1)
RoundtableSession >── Host (Speaker, many:1)
```

Full field-level schema is in `cms-database-schema.md`.

## 5. Design System Reuse Map

Every interior page reuses, never reinvents:
- `.page-hero` pattern (eyebrow + h1 + sub, on `--bg-light`) for page intros — Home uses its own larger split hero since it's the flagship entry point.
- `.card` for all listing items (speakers, sessions, articles, partners).
- `.cta-banner` (3-up) and `.cta-final` (dark full-bleed) for end-of-page conversion — alternated by page so the site doesn't feel repetitive while staying systemized.
- `.filter-bar` for every listing page (Agenda, Speakers, Insights, Partners logo wall).
- Same nav/footer markup site-wide, generated from a single layout component in the Next.js implementation (`components/Layout.tsx`), not duplicated per page.

## 6. Routing & Rendering Strategy (Next.js + Headless CMS)

- **SSG + ISR** for Speakers, Partners, Insights, Agenda — content changes occasionally, not per-request. Revalidate every 60s so CMS publishes go live without a redeploy.
- **SSR** for `/request-invitation/status` — depends on submitted application state.
- **Client-side** form handling for Request Invitation and Contact, posting to API routes that forward to CMS/CRM (e.g., `/api/invitations`, `/api/contact`) and trigger the approval workflow.
- **Dynamic routes**: `[speaker-slug]`, `[partner-slug]`, `[article-slug]` resolved via `getStaticPaths` from CMS collection queries, with `fallback: 'blocking'` so newly published entries resolve without a full rebuild.
