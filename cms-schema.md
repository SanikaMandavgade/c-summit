# The Guest Experience Summit — CMS Field Schema
### Content-editor-facing field definitions (Next.js + Headless CMS)

This document covers every CMS-managed collection: the fields a content editor sees and fills in. Transactional/database-only tables (form submissions, applications) are in `database-schema.md`.

---

## Event & EventDay

**Event**
| Field | Type | Notes |
|---|---|---|
| name | string | "The Guest Experience Summit 2027" |
| slug | string | `summit-2027` |
| startDate / endDate | date | |
| venueName | string | "The Landmark, London" |
| venueAddress | text | |
| heroHeadline | string | |
| heroSubheadline | string | |
| heroImage | media | |
| statAttendees / statSpeakers / statPartners / statCountries | number | drives home stats bar |
| isActive | boolean | flags current/live edition |

**EventDay**
| Field | Type | Notes |
|---|---|---|
| event | reference → Event | |
| label | string | "Day 1 — Main Summit" |
| date | date | |
| slug | string | `day-1` |
| sortOrder | number | |

---

## Track, SessionCategory, Session

**Track**
| Field | Type | Notes |
|---|---|---|
| name | string | "Track A — Guest Experience & Technology" |
| slug | string | |
| colorHex | string | badge/dot rendering |
| sortOrder | number | |

**SessionCategory**
| Field | Type | Notes |
|---|---|---|
| name | string | Keynote / Panel / Fireside Chat / Workshop / Break / Networking |
| slug | string | |

**Session**
| Field | Type | Notes |
|---|---|---|
| eventDay | reference → EventDay | |
| track | reference → Track, nullable | null = Mainstage |
| category | reference → SessionCategory | |
| title | string | |
| slug | string | |
| description | richtext | |
| startTime / endTime | time | |
| room | string | |
| speakers | reference[] → Speaker | many-to-many |
| isBreak | boolean | muted "break" card style |
| agendaOrder | number | |

---

## Company & Speaker

**Company**
| Field | Type | Notes |
|---|---|---|
| name | string | |
| slug | string | |
| logo | media | |
| website | url | |
| industry | reference → PartnerCategory, nullable | |

**SpeakerCategory** (CEO, COO, CMO, Technology, Hospitality, Investment)
| Field | Type |
|---|---|
| name | string |
| slug | string |

**Speaker**
| Field | Type | Notes |
|---|---|---|
| name | string | |
| slug | string | |
| jobTitle | string | |
| company | reference → Company | |
| headshot | media | |
| bio | richtext | profile page, 2–3 paragraphs |
| shortBio | text | card excerpt |
| linkedinUrl | url | |
| categories | reference[] → SpeakerCategory | |
| isFeaturedKeynote | boolean | drives "Featured" row |
| basedInCity / basedInCountry | string | |

---

## Partner, PartnerTier, PartnerCategory, SponsorshipPackage

**PartnerTier**
| Field | Type |
|---|---|
| name | string |
| slug | string |
| sortOrder | number |

**PartnerCategory** (Technology / Finance / Services / Design)
| Field | Type |
|---|---|
| name | string |
| slug | string |

**SponsorshipPackage**
| Field | Type | Notes |
|---|---|---|
| tier | reference → PartnerTier | |
| priceDisplay | string | "£42,000" / "By Application" |
| description | text | |
| features | string[] | bullet list |
| isFeatured | boolean | "Most Popular" ribbon |

**Partner**
| Field | Type | Notes |
|---|---|---|
| company | reference → Company | |
| slug | string | |
| tier | reference → PartnerTier | |
| categories | reference[] → PartnerCategory | |
| logo | media | |
| description | richtext | |
| caseStudyQuote | text | nullable |
| caseStudyAuthor | reference → Speaker, nullable | |
| isActive | boolean | controls logo wall visibility |

---

## Insights: Article, Author, ArticleCategory, Tag

**ArticleCategory** (Technology, Investment, Operations, Leadership, Brand)
| Field | Type |
|---|---|
| name | string |
| slug | string |

**Tag**
| Field | Type |
|---|---|
| name | string |
| slug | string |

**Author**
| Field | Type | Notes |
|---|---|---|
| name | string | |
| photo | media | |
| roleAndCompany | string | |
| linkedSpeaker | reference → Speaker, nullable | |

**Article**
| Field | Type | Notes |
|---|---|---|
| title | string | |
| slug | string | |
| contentType | enum | Article / Report / Whitepaper / Video / Podcast |
| coverImage | media | |
| excerpt | text | |
| body | richtext | |
| author | reference → Author | |
| category | reference → ArticleCategory | |
| tags | reference[] → Tag | |
| readTimeMinutes | number | |
| publishedAt | date | |
| isFeatured | boolean | listing-page hero slot |
| downloadAsset | file, nullable | PDF |
| videoUrl | url, nullable | |
| audioUrl | url, nullable | |

---

## Networking

**RoundtableSession**
| Field | Type | Notes |
|---|---|---|
| title | string | |
| host | reference → Speaker | |
| description | text | |
| capacity | number | default 6 |
| seatsRemaining | number | computed |
| time | datetime | |

---

## About page

**TeamMember**
| Field | Type |
|---|---|
| name | string |
| role | string |
| photo | media |
| sortOrder | number |

**TimelineMilestone**
| Field | Type |
|---|---|
| year | string |
| title | string |
| description | text |
| sortOrder | number |

---

## Contact

**Office**
| Field | Type |
|---|---|
| name | string |
| addressLine | text |
| isHQ | boolean |

**ContactDepartment**
| Field | Type |
|---|---|
| name | string |
| email | string |
| description | string |

**FAQItem**
| Field | Type |
|---|---|
| question | string |
| answer | richtext |
| appliesToPage | enum[] | Contact / RequestInvitation / Both |
| sortOrder | number |

---

## SiteSettings (singleton)

| Field | Type | Notes |
|---|---|---|
| logoText | string | |
| navLinks | array of {label, url} | |
| footerColumns | array of {heading, links[]} | |
| socialLinks | array of {platform, url} | |

Single source for Nav + Footer, consumed by `components/Layout.tsx` so nav/footer edits happen once in the CMS.
