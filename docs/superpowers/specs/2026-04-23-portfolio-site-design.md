---
title: Portfolio Site — Design Spec
author: Harsha Jaiganesh
date: 2026-04-23
status: draft
---

# Portfolio Site — Design Spec

## Purpose

A recruiter-facing portfolio site for Harsha Jaiganesh. Primary audience: recruiters and hiring managers. Primary goal: support active job hunting by presenting resume, experience, and three flagship projects in a memorable, themed, but still professional package.

## Non-Goals

- No blog, no CMS, no comments
- No analytics beyond what the host provides by default
- No i18n, no dark/light toggle (the site is dark by design)
- No backend, no authentication, no dynamic content

## Audience and Tone

Recruiters first. The theme (Naruto, medium intensity) is polish, not scaffolding — with CSS disabled the page must still read as a straightforward resume. Theme vocabulary is used as flavor; all UI still makes sense to a reader who has never heard of Naruto.

## Stack

- **Framework:** Astro (latest, static output)
- **Styling:** Tailwind CSS
- **Language:** TypeScript
- **Hosting:** Netlify free tier, default subdomain (e.g. `harsha-jaiganesh.netlify.app`). Custom domain deferred.
- **Build:** `npm run build` produces static `dist/`; Netlify builds on push.

Astro chosen over Next.js and plain static because it gives component-based authoring with zero runtime JS by default — fast Lighthouse scores with no effort, and trivial to add a blog later if needed.

## Directory Layout

```
portfolio/
├── src/
│   ├── pages/
│   │   └── index.astro              single-page site
│   ├── layouts/
│   │   └── Base.astro               shared head, fonts, meta
│   ├── components/
│   │   ├── Hero.astro
│   │   ├── About.astro
│   │   ├── MissionLog.astro         experience timeline
│   │   ├── Missions.astro           projects grid
│   │   ├── Jutsu.astro              skills
│   │   ├── Scroll.astro             resume preview + download
│   │   ├── MessengerHawk.astro      contact
│   │   └── KanjiWatermark.astro     reusable per-section motif
│   ├── content/
│   │   ├── projects.json
│   │   └── experience.json
│   └── styles/
│       └── global.css               Tailwind entry + theme tokens
├── public/
│   ├── resume.pdf                   copied from Harsha_Jaiganesh_Resume copy.pdf
│   └── fonts/                       self-hosted webfonts
├── astro.config.mjs
├── tailwind.config.mjs
├── tsconfig.json
└── package.json
```

`portfolio/` sits inside `/Users/bobbydylan/Projects/` alongside the featured project folders.

## Visual System

### Palette (Tailwind tokens)

| Token        | Hex        | Use                              |
|--------------|------------|----------------------------------|
| `ink`        | `#0f0e0c`  | page background                  |
| `paper`      | `#f4e8d0`  | primary text                     |
| `konoha`     | `#e97917`  | single accent, CTAs, dividers    |
| `tan`        | `#c9b88a`  | secondary text, muted UI         |
| `ink-2`      | `#1a1410`  | raised surfaces (cards)          |

### Typography

- **Headings:** IBM Plex Serif (700) — gives the scroll/paper feel
- **Body:** Inter (400/500) — recruiter-readable
- **Kanji accents only:** Noto Sans JP (700) — never used for Latin copy
- All fonts self-hosted from `public/fonts/` (no external requests)

### Motifs (used sparingly)

- Faded large kanji watermark per section at ~8% opacity: 忍 (hero), 任務 (missions), 術 (jutsu), 巻物 (scroll), 手紙 (messenger hawk)
- Thin 1px konoha-orange rule as section divider
- Rank badges (Genin/Chunin/Jonin/S-Rank) used semantically: years of experience on About, difficulty on project cards

### Motion

One ink-reveal fade on hero load. No autoplay, no scroll-jacking, no spinners. `prefers-reduced-motion: reduce` disables all motion.

## Vocabulary Mapping

| Standard section | Site label            | Kanji  |
|------------------|-----------------------|--------|
| Hero             | Hero                  | 忍     |
| About            | Nindo (the ninja way) | 道     |
| Experience       | Mission Log           | 任務   |
| Projects         | Missions              | 巻物   |
| Skills           | Jutsu                 | 術     |
| Resume           | Scroll                | 巻物   |
| Contact          | Messenger Hawk        | 手紙   |

Each section label is accompanied by the plain-English equivalent in smaller tan text on first render, so non-fans are never confused.

## Sections

### Hero

- Name: **Harsha Jaiganesh**
- Tagline: to pick one of (spec asks user to select):
  1. "Full-stack and AI engineer shipping shinobi-grade software."
  2. "I build AI-powered apps end to end — native, web, and everything between."
  3. "Engineer building at the edge of AI, product, and polish."
- Primary CTA: `▸ View Missions` → scrolls to projects
- Secondary CTA: `Download Scroll` → `/resume.pdf`
- Background: 忍 kanji watermark, single horizontal orange rule

### Nindo (About)

- 2–3 short paragraphs drafted by the builder from the resume + project READMEs, then user-edited
- One `rank` label computed from years of experience (resume-derived)
- No stock imagery

### Mission Log (Experience)

- Vertical timeline, most recent first
- Each entry: company, role, dates, 2–3 bullet impact statements
- Data source: `src/content/experience.json`, populated once during setup by extracting text from `Harsha_Jaiganesh_Resume copy.pdf` with `pdftotext` and structuring by hand
- User reviews and approves the extracted JSON before it ships

### Missions (Projects)

Three cards, in this order:

1. **l8ter** — iOS reel-inbox, Swift/SwiftUI, Claude API, CoreLocation geofences. Rank: A.
2. **Kanban PM** — full-stack Kanban app, JWT auth, AI chat assistant. Rank: B.
3. **prelegal** — AI legal-doc drafting platform, custom amber-CRT UI. Rank: S.

Each card:

- Title, one-line hook, stack badges (max 5), rank label, `source` link to GitHub, `live` link if applicable
- Hook copy drafted from each project's README, user-editable in `src/content/projects.json`
- Rank is a design/flavor label, never claimed as an objective metric

Course repos (`agents`, `llm_engineering`) and `finally` are intentionally excluded.

### Jutsu (Skills)

Groups (populated from resume + project stacks):

- **Languages**
- **Frameworks**
- **AI / LLM**
- **Infrastructure**

Chips only — no percentage bars, no "expert/intermediate" self-ratings (recruiter-noise).

### Scroll (Resume)

- Inline PDF preview via `<object>` with fallback download link
- Primary download button → `/resume.pdf`
- Source file: `Harsha_Jaiganesh_Resume copy.pdf` copied into `public/resume.pdf` at setup time

### Messenger Hawk (Contact)

- Email: `jaiganeshharsha@gmail.com` (mailto link)
- LinkedIn: `https://www.linkedin.com/in/harsha-jaiganesh/`
- GitHub: `https://github.com/Harsha03`
- Three tiles, orange accent on hover

## Data Flow

All content is build-time static. Two JSON files (`projects.json`, `experience.json`) are the sole editable content surface. Astro's content collections provide a TypeScript schema so a missing field fails the build instead of shipping broken UI.

No runtime fetches, no API calls, no client-side state.

## Accessibility

- Semantic HTML (`<header>`, `<main>`, `<section>`, `<nav>`, `<footer>`)
- All orange/cream pairs meet WCAG AA contrast (verified in CI via Lighthouse)
- Focus rings visible (2px konoha outline)
- Kanji marked `aria-hidden="true"` since it's decorative
- All imagery has alt text; decorative imagery has empty alt
- Reduced motion honored

## Testing

- `tsc --noEmit` and `astro check` on build
- Lighthouse target: ≥95 on Performance, Accessibility, Best Practices, SEO
- Manual smoke checklist before first deploy:
  - All nav links scroll correctly
  - Resume renders inline and downloads
  - All external links open in new tab with `rel="noopener"`
  - Mobile viewport (375px) has no horizontal scroll
  - Works with CSS disabled (still readable as a plain-text resume)
  - Works with JavaScript disabled (Astro ships zero JS by default; confirms we stayed in that lane)

## Build Sequence (high-level; detailed plan comes next)

1. Scaffold Astro + Tailwind + TypeScript in `portfolio/`
2. Copy resume PDF, extract experience with `pdftotext`, fill `experience.json`
3. Build `Base.astro` layout with theme tokens and fonts
4. Build each section component in order: Hero, Nindo, Mission Log, Missions, Jutsu, Scroll, Messenger Hawk
5. Populate `projects.json` with three projects, author hooks from READMEs
6. Accessibility + Lighthouse pass
7. Deploy to Netlify, share URL

## Open Items (user action)

- Pick a tagline from the three options above (or write your own)
- Review the extracted `experience.json` before first deploy
- Approve the About copy draft before first deploy
