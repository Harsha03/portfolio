# Portfolio Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a recruiter-facing, Naruto-themed (medium intensity) single-page portfolio for Harsha Jaiganesh featuring three projects, resume download, and contact links, deployed to Netlify.

**Architecture:** Static Astro site with Tailwind, TypeScript, and build-time JSON content collections. Zero runtime JavaScript by default. Single-page layout with anchored sections. Theme expressed via custom Tailwind tokens, self-hosted fonts (IBM Plex Serif, Inter, Noto Sans JP), and kanji watermarks.

**Tech Stack:** Astro, Tailwind CSS, TypeScript, Netlify.

**Working directory:** `/Users/bobbydylan/Projects/portfolio/`

---

## File Structure (locked before tasks)

```
portfolio/
├── src/
│   ├── pages/index.astro
│   ├── layouts/Base.astro
│   ├── components/
│   │   ├── Hero.astro
│   │   ├── Nindo.astro
│   │   ├── MissionLog.astro
│   │   ├── Missions.astro
│   │   ├── Jutsu.astro
│   │   ├── Scroll.astro
│   │   ├── MessengerHawk.astro
│   │   ├── SectionHeader.astro        (reused label + kanji watermark)
│   │   └── Nav.astro
│   ├── content/
│   │   ├── config.ts                  (Astro content schemas)
│   │   ├── projects.json
│   │   └── experience.json
│   └── styles/global.css
├── public/
│   ├── resume.pdf
│   └── fonts/
│       ├── IBMPlexSerif-Bold.woff2
│       ├── IBMPlexSerif-Regular.woff2
│       ├── Inter-Regular.woff2
│       ├── Inter-Medium.woff2
│       └── NotoSansJP-Bold.woff2
├── astro.config.mjs
├── tailwind.config.mjs
├── tsconfig.json
├── package.json
├── netlify.toml
└── .gitignore
```

---

### Task 1: Scaffold Astro project with TypeScript and Tailwind

**Files:**
- Create: `portfolio/package.json`, `portfolio/astro.config.mjs`, `portfolio/tailwind.config.mjs`, `portfolio/tsconfig.json`, `portfolio/src/pages/index.astro`, `portfolio/src/styles/global.css`

- [ ] **Step 1: Initialize Astro project in the existing `portfolio/` directory (non-interactive)**

Run from `/Users/bobbydylan/Projects/portfolio`:

```bash
npm create astro@latest . -- --template minimal --typescript strict --install --no-git --yes
```

Expected: Astro scaffolds into the current directory without overwriting `docs/`. If it refuses due to non-empty directory, create a temp scaffold elsewhere and copy files in, preserving `docs/` and `.git/`.

- [ ] **Step 2: Add Tailwind integration**

Run:

```bash
npx astro add tailwind --yes
```

Expected: `@astrojs/tailwind` added to deps and `astro.config.mjs`, `tailwind.config.mjs` created.

- [ ] **Step 3: Verify build works**

Run:

```bash
npm run build
```

Expected: Exits 0, produces `dist/index.html`.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "scaffold: astro + typescript + tailwind"
```

---

### Task 2: Configure theme tokens in Tailwind

**Files:**
- Modify: `portfolio/tailwind.config.mjs`
- Modify: `portfolio/src/styles/global.css`

- [ ] **Step 1: Replace `tailwind.config.mjs` content**

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./src/**/*.{astro,html,ts,tsx}'],
  theme: {
    extend: {
      colors: {
        ink: '#0f0e0c',
        'ink-2': '#1a1410',
        paper: '#f4e8d0',
        konoha: '#e97917',
        tan: '#c9b88a',
      },
      fontFamily: {
        serif: ['"IBM Plex Serif"', 'Georgia', 'serif'],
        sans: ['Inter', 'system-ui', 'sans-serif'],
        jp: ['"Noto Sans JP"', 'sans-serif'],
      },
      maxWidth: {
        prose: '68ch',
        page: '1100px',
      },
    },
  },
};
```

- [ ] **Step 2: Replace `src/styles/global.css`**

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@font-face { font-family: 'IBM Plex Serif'; src: url('/fonts/IBMPlexSerif-Regular.woff2') format('woff2'); font-weight: 400; font-display: swap; }
@font-face { font-family: 'IBM Plex Serif'; src: url('/fonts/IBMPlexSerif-Bold.woff2') format('woff2'); font-weight: 700; font-display: swap; }
@font-face { font-family: 'Inter'; src: url('/fonts/Inter-Regular.woff2') format('woff2'); font-weight: 400; font-display: swap; }
@font-face { font-family: 'Inter'; src: url('/fonts/Inter-Medium.woff2') format('woff2'); font-weight: 500; font-display: swap; }
@font-face { font-family: 'Noto Sans JP'; src: url('/fonts/NotoSansJP-Bold.woff2') format('woff2'); font-weight: 700; font-display: swap; }

:root { color-scheme: dark; }
html, body { background: #0f0e0c; color: #f4e8d0; }
body { font-family: Inter, system-ui, sans-serif; }
h1, h2, h3 { font-family: 'IBM Plex Serif', Georgia, serif; }

:focus-visible { outline: 2px solid #e97917; outline-offset: 2px; }

@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation: none !important; transition: none !important; }
}
```

- [ ] **Step 3: Download webfonts into `public/fonts/`**

```bash
mkdir -p public/fonts
cd public/fonts
curl -sLO https://cdn.jsdelivr.net/fontsource/fonts/ibm-plex-serif@latest/latin-400-normal.woff2 && mv latin-400-normal.woff2 IBMPlexSerif-Regular.woff2
curl -sLO https://cdn.jsdelivr.net/fontsource/fonts/ibm-plex-serif@latest/latin-700-normal.woff2 && mv latin-700-normal.woff2 IBMPlexSerif-Bold.woff2
curl -sLO https://cdn.jsdelivr.net/fontsource/fonts/inter@latest/latin-400-normal.woff2 && mv latin-400-normal.woff2 Inter-Regular.woff2
curl -sLO https://cdn.jsdelivr.net/fontsource/fonts/inter@latest/latin-500-normal.woff2 && mv latin-500-normal.woff2 Inter-Medium.woff2
curl -sLO https://cdn.jsdelivr.net/fontsource/fonts/noto-sans-jp@latest/japanese-700-normal.woff2 && mv japanese-700-normal.woff2 NotoSansJP-Bold.woff2
cd ../..
```

Expected: Five `.woff2` files exist under `public/fonts/`. If the fontsource URL fails, fall back to Google Fonts static downloads and document URLs in commit.

- [ ] **Step 4: Verify build still passes**

Run:

```bash
npm run build
```

Expected: Exit 0.

- [ ] **Step 5: Commit**

```bash
git add tailwind.config.mjs src/styles/global.css public/fonts/
git commit -m "theme: naruto palette, typography, self-hosted fonts"
```

---

### Task 3: Copy resume PDF into `public/`

**Files:**
- Create: `portfolio/public/resume.pdf`

- [ ] **Step 1: Copy and rename the resume**

```bash
cp "/Users/bobbydylan/Projects/Harsha_Jaiganesh_Resume copy.pdf" public/resume.pdf
```

- [ ] **Step 2: Verify size > 10KB**

```bash
ls -la public/resume.pdf
```

Expected: File ~156KB.

- [ ] **Step 3: Commit**

```bash
git add public/resume.pdf
git commit -m "content: add resume pdf"
```

---

### Task 4: Extract experience from resume into `experience.json`

**Files:**
- Create: `portfolio/src/content/experience.json`

- [ ] **Step 1: Extract text from the PDF**

```bash
pdftotext -layout public/resume.pdf /tmp/resume.txt
cat /tmp/resume.txt
```

Expected: Plain text dump of resume contents. If `pdftotext` is missing, install with `brew install poppler`.

- [ ] **Step 2: Structure into JSON**

Hand-author `src/content/experience.json` using the extracted text. Shape:

```json
{
  "entries": [
    {
      "company": "Company Name",
      "role": "Role Title",
      "start": "2023-06",
      "end": "Present",
      "location": "City, State",
      "bullets": [
        "Impact-oriented bullet one.",
        "Impact-oriented bullet two."
      ]
    }
  ]
}
```

Populate every role found in the resume. Preserve original impact phrasing. Order: most recent first.

- [ ] **Step 3: Commit**

```bash
git add src/content/experience.json
git commit -m "content: structured experience from resume"
```

---

### Task 5: Author `projects.json` with three featured projects

**Files:**
- Create: `portfolio/src/content/projects.json`

- [ ] **Step 1: Write the file**

```json
{
  "projects": [
    {
      "slug": "l8ter",
      "title": "l8ter",
      "rank": "A",
      "hook": "iOS inbox for TikTok reels — Claude categorizes, CoreLocation nudges you when you walk past a saved spot.",
      "stack": ["Swift", "SwiftUI", "SwiftData", "Claude API", "CoreLocation"],
      "source": "https://github.com/Harsha03/location",
      "live": null,
      "summary": "Native iOS app that captures TikTok reels, uses Claude Haiku 4.5 to categorize into 9 types (restaurant, movie, recipe, etc.), enriches via web_search, and geofences up to 20 saved places for proximity alerts."
    },
    {
      "slug": "kanban-pm",
      "title": "Kanban PM",
      "rank": "B",
      "hook": "Full-stack multi-board Kanban with JWT auth, drag-and-drop, and an optional AI chat assistant.",
      "stack": ["Next.js", "FastAPI", "SQLite", "JWT", "Docker"],
      "source": "https://github.com/Harsha03/pm",
      "live": null,
      "summary": "Multi-board project management app: drag-and-drop cards, board templates (Kanban/Scrum/Bug Tracking), JSON export/import, per-user isolation, optional AI chat that can modify boards."
    },
    {
      "slug": "prelegal",
      "title": "prelegal",
      "rank": "S",
      "hook": "AI-guided legal document drafting on 12 Common Paper templates, with a custom amber-CRT terminal UI.",
      "stack": ["Next.js", "FastAPI", "SQLite", "LLM", "Docker"],
      "source": "https://github.com/Harsha03/prelegal",
      "live": null,
      "summary": "Pick a template (NDA, SaaS Agreement, DPA, BAA, PSA, SLA…), AI chat guides you field-by-field, live markdown preview, PDF export. Custom visual direction: CRT amber chrome around a cream serif preview."
    }
  ]
}
```

- [ ] **Step 2: Commit**

```bash
git add src/content/projects.json
git commit -m "content: seed three featured projects"
```

---

### Task 6: Set up Astro content collection schemas

**Files:**
- Create: `portfolio/src/content/config.ts`

- [ ] **Step 1: Write the schema file**

```ts
import { defineCollection, z } from 'astro:content';

const projects = defineCollection({
  type: 'data',
  schema: z.object({
    projects: z.array(z.object({
      slug: z.string(),
      title: z.string(),
      rank: z.enum(['S', 'A', 'B', 'C']),
      hook: z.string(),
      stack: z.array(z.string()).max(6),
      source: z.string().url(),
      live: z.string().url().nullable(),
      summary: z.string(),
    })),
  }),
});

const experience = defineCollection({
  type: 'data',
  schema: z.object({
    entries: z.array(z.object({
      company: z.string(),
      role: z.string(),
      start: z.string(),
      end: z.string(),
      location: z.string().optional(),
      bullets: z.array(z.string()).min(1).max(5),
    })),
  }),
});

export const collections = { projects, experience };
```

- [ ] **Step 2: Verify type-check**

```bash
npm run build
```

Expected: Build passes. If schema mismatch, fix the JSON until it validates.

- [ ] **Step 3: Commit**

```bash
git add src/content/config.ts
git commit -m "content: astro collection schemas for projects and experience"
```

---

### Task 7: Build `Base.astro` layout

**Files:**
- Create: `portfolio/src/layouts/Base.astro`

- [ ] **Step 1: Write the file**

```astro
---
import '../styles/global.css';
interface Props { title: string; description?: string; }
const { title, description = 'Harsha Jaiganesh — engineer and builder.' } = Astro.props;
---
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <meta name="description" content={description} />
    <meta name="theme-color" content="#0f0e0c" />
    <title>{title}</title>
    <link rel="preload" href="/fonts/IBMPlexSerif-Bold.woff2" as="font" type="font/woff2" crossorigin />
    <link rel="preload" href="/fonts/Inter-Regular.woff2" as="font" type="font/woff2" crossorigin />
  </head>
  <body class="bg-ink text-paper antialiased">
    <slot />
  </body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add src/layouts/Base.astro
git commit -m "layout: base template with fonts preloaded"
```

---

### Task 8: Build reusable `SectionHeader.astro`

**Files:**
- Create: `portfolio/src/components/SectionHeader.astro`

- [ ] **Step 1: Write the file**

```astro
---
interface Props {
  label: string;
  plain: string;
  kanji: string;
  id: string;
}
const { label, plain, kanji, id } = Astro.props;
---
<header id={id} class="relative mx-auto max-w-page px-6 pt-24 pb-8">
  <span aria-hidden="true" class="pointer-events-none absolute -top-8 right-4 select-none font-jp text-[9rem] leading-none text-konoha/[0.08]">{kanji}</span>
  <div class="flex items-baseline gap-3">
    <h2 class="text-3xl font-bold text-paper">{label}</h2>
    <span class="text-xs uppercase tracking-[0.2em] text-tan">{plain}</span>
  </div>
  <div class="mt-4 h-px w-16 bg-konoha"></div>
</header>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/SectionHeader.astro
git commit -m "component: SectionHeader with kanji watermark"
```

---

### Task 9: Build `Nav.astro`

**Files:**
- Create: `portfolio/src/components/Nav.astro`

- [ ] **Step 1: Write the file**

```astro
---
const items = [
  { href: '#nindo',   label: 'Nindo' },
  { href: '#log',     label: 'Mission Log' },
  { href: '#missions',label: 'Missions' },
  { href: '#jutsu',   label: 'Jutsu' },
  { href: '#scroll',  label: 'Scroll' },
  { href: '#hawk',    label: 'Contact' },
];
---
<nav class="sticky top-0 z-40 border-b border-tan/10 bg-ink/80 backdrop-blur">
  <div class="mx-auto flex max-w-page items-center justify-between px-6 py-3 text-sm">
    <a href="#top" class="font-serif text-base font-bold text-paper">Harsha Jaiganesh</a>
    <ul class="flex gap-5 text-tan">
      {items.map(i => (
        <li><a href={i.href} class="hover:text-konoha transition-colors">{i.label}</a></li>
      ))}
    </ul>
  </div>
</nav>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Nav.astro
git commit -m "component: sticky nav"
```

---

### Task 10: Build `Hero.astro`

**Files:**
- Create: `portfolio/src/components/Hero.astro`

- [ ] **Step 1: Write the file (pick tagline index via TAGLINE constant)**

```astro
---
const taglines = [
  'Full-stack and AI engineer shipping shinobi-grade software.',
  'I build AI-powered apps end to end — native, web, and everything between.',
  'Engineer building at the edge of AI, product, and polish.',
];
const tagline = taglines[0];
---
<section id="top" class="relative overflow-hidden">
  <span aria-hidden="true" class="pointer-events-none absolute -top-12 -right-10 select-none font-jp text-[22rem] leading-none text-konoha/[0.07]">忍</span>
  <div class="mx-auto max-w-page px-6 pt-28 pb-20">
    <p class="text-xs uppercase tracking-[0.3em] text-konoha">Konoha · 木ノ葉</p>
    <h1 class="mt-4 font-serif text-6xl font-bold leading-tight text-paper md:text-7xl">Harsha Jaiganesh</h1>
    <p class="mt-5 max-w-prose text-lg text-tan">{tagline}</p>
    <div class="mt-8 flex flex-wrap gap-3">
      <a href="#missions" class="inline-block bg-konoha px-5 py-2.5 text-sm font-medium text-ink hover:brightness-110">▸ View Missions</a>
      <a href="/resume.pdf" class="inline-block border border-tan px-5 py-2.5 text-sm font-medium text-paper hover:border-konoha hover:text-konoha">Download Scroll</a>
    </div>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Hero.astro
git commit -m "component: Hero with tagline and CTAs"
```

---

### Task 11: Build `Nindo.astro` (About)

**Files:**
- Create: `portfolio/src/components/Nindo.astro`

- [ ] **Step 1: Write the file**

```astro
---
import SectionHeader from './SectionHeader.astro';
---
<section>
  <SectionHeader id="nindo" label="Nindo" plain="The Ninja Way · About" kanji="道" />
  <div class="mx-auto max-w-prose px-6 pb-8 text-paper/90 space-y-4 text-lg leading-relaxed">
    <p>
      I build products that sit at the intersection of AI, native mobile, and well-crafted web interfaces.
      Most recently: an iOS reel-inbox that uses Claude to categorize and geofence real-world places,
      a full-stack Kanban tool with an AI assistant, and an AI-guided legal drafting platform.
    </p>
    <p>
      I care about craft — the kind that shows up in a thoughtful empty state, a
      measured motion curve, and a well-named function. Recruiter-friendly translation:
      I ship end-to-end features, from database to pixel, and I pick my abstractions carefully.
    </p>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Nindo.astro
git commit -m "component: Nindo (about section)"
```

---

### Task 12: Build `MissionLog.astro` (Experience timeline)

**Files:**
- Create: `portfolio/src/components/MissionLog.astro`

- [ ] **Step 1: Write the file**

```astro
---
import { getEntry } from 'astro:content';
import SectionHeader from './SectionHeader.astro';
const data = await getEntry('experience', 'experience');
const entries = data?.data.entries ?? [];
---
<section>
  <SectionHeader id="log" label="Mission Log" plain="Experience" kanji="任務" />
  <ol class="mx-auto max-w-page px-6 pb-12">
    {entries.map(e => (
      <li class="relative border-l border-tan/30 pl-6 py-4">
        <span class="absolute -left-1.5 top-6 h-3 w-3 rounded-full bg-konoha"></span>
        <div class="flex flex-wrap items-baseline justify-between gap-2">
          <h3 class="font-serif text-xl text-paper">{e.role} · <span class="text-tan">{e.company}</span></h3>
          <span class="text-xs uppercase tracking-wider text-tan">{e.start} — {e.end}</span>
        </div>
        {e.location && <p class="text-xs text-tan/80">{e.location}</p>}
        <ul class="mt-2 list-disc pl-5 space-y-1 text-paper/90">
          {e.bullets.map(b => <li>{b}</li>)}
        </ul>
      </li>
    ))}
  </ol>
</section>
```

Note: `getEntry('experience', 'experience')` expects the JSON to be at `src/content/experience/experience.json`. Move `src/content/experience.json` → `src/content/experience/experience.json` and `src/content/projects.json` → `src/content/projects/projects.json` to match Astro's collection-per-folder convention.

- [ ] **Step 2: Restructure content folder**

```bash
mkdir -p src/content/experience src/content/projects
git mv src/content/experience.json src/content/experience/experience.json
git mv src/content/projects.json src/content/projects/projects.json
```

- [ ] **Step 3: Build**

```bash
npm run build
```

Expected: Build passes, entries render to static HTML.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "component: MissionLog timeline + restructure content dirs"
```

---

### Task 13: Build `Missions.astro` (Projects grid)

**Files:**
- Create: `portfolio/src/components/Missions.astro`

- [ ] **Step 1: Write the file**

```astro
---
import { getEntry } from 'astro:content';
import SectionHeader from './SectionHeader.astro';
const data = await getEntry('projects', 'projects');
const projects = data?.data.projects ?? [];
const rankColor: Record<string, string> = { S: 'text-konoha border-konoha', A: 'text-paper border-paper', B: 'text-tan border-tan', C: 'text-tan/70 border-tan/70' };
---
<section>
  <SectionHeader id="missions" label="Missions" plain="Projects" kanji="巻物" />
  <div class="mx-auto grid max-w-page gap-6 px-6 pb-12 md:grid-cols-3">
    {projects.map(p => (
      <article class="flex flex-col border border-tan/20 bg-ink-2 p-6 hover:border-konoha/60 transition-colors">
        <div class="flex items-start justify-between">
          <h3 class="font-serif text-2xl text-paper">{p.title}</h3>
          <span class={`border px-2 py-0.5 text-xs font-bold ${rankColor[p.rank]}`}>{p.rank}-RANK</span>
        </div>
        <p class="mt-3 text-paper/90">{p.hook}</p>
        <ul class="mt-4 flex flex-wrap gap-1.5">
          {p.stack.map(s => <li class="border border-tan/30 px-2 py-0.5 text-xs text-tan">{s}</li>)}
        </ul>
        <p class="mt-4 text-sm text-paper/70">{p.summary}</p>
        <div class="mt-auto pt-5 flex gap-4 text-sm">
          <a href={p.source} class="text-konoha hover:underline" rel="noopener" target="_blank">Source ↗</a>
          {p.live && <a href={p.live} class="text-konoha hover:underline" rel="noopener" target="_blank">Live ↗</a>}
        </div>
      </article>
    ))}
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Missions.astro
git commit -m "component: Missions projects grid"
```

---

### Task 14: Build `Jutsu.astro` (Skills)

**Files:**
- Create: `portfolio/src/components/Jutsu.astro`

- [ ] **Step 1: Write the file. Edit the `skills` object after extracting real skills from resume in Task 4.**

```astro
---
import SectionHeader from './SectionHeader.astro';
const skills: Record<string, string[]> = {
  Languages:     ['TypeScript', 'Python', 'Swift', 'JavaScript', 'SQL'],
  Frameworks:    ['Next.js', 'React', 'Astro', 'FastAPI', 'SwiftUI', 'Tailwind'],
  'AI / LLM':    ['Claude API', 'OpenAI', 'LiteLLM', 'Prompt caching', 'Tool use', 'Structured outputs'],
  Infrastructure:['Docker', 'SQLite', 'SSE', 'JWT', 'Netlify', 'Vercel'],
};
---
<section>
  <SectionHeader id="jutsu" label="Jutsu" plain="Skills" kanji="術" />
  <div class="mx-auto max-w-page px-6 pb-12 space-y-6">
    {Object.entries(skills).map(([group, items]) => (
      <div>
        <h3 class="mb-2 text-xs uppercase tracking-[0.2em] text-tan">{group}</h3>
        <ul class="flex flex-wrap gap-2">
          {items.map(s => <li class="border border-tan/30 bg-ink-2 px-3 py-1 text-sm text-paper">{s}</li>)}
        </ul>
      </div>
    ))}
  </div>
</section>
```

Note: After resume extraction in Task 4 completes, revise this list to exactly match the resume. Remove anything not on the resume; add anything listed there that we missed.

- [ ] **Step 2: Commit**

```bash
git add src/components/Jutsu.astro
git commit -m "component: Jutsu skills section"
```

---

### Task 15: Build `Scroll.astro` (Resume)

**Files:**
- Create: `portfolio/src/components/Scroll.astro`

- [ ] **Step 1: Write the file**

```astro
---
import SectionHeader from './SectionHeader.astro';
---
<section>
  <SectionHeader id="scroll" label="Scroll" plain="Resume" kanji="巻物" />
  <div class="mx-auto max-w-page px-6 pb-12">
    <div class="mb-4 flex items-center justify-between">
      <p class="text-tan">Preview below, or download the PDF.</p>
      <a href="/resume.pdf" download class="inline-block bg-konoha px-4 py-2 text-sm font-medium text-ink hover:brightness-110">Download PDF</a>
    </div>
    <object data="/resume.pdf" type="application/pdf" class="h-[80vh] w-full border border-tan/20 bg-ink-2">
      <p class="p-6 text-paper">Your browser can't embed PDFs. <a class="text-konoha underline" href="/resume.pdf">Download the resume</a>.</p>
    </object>
  </div>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Scroll.astro
git commit -m "component: Scroll resume preview + download"
```

---

### Task 16: Build `MessengerHawk.astro` (Contact)

**Files:**
- Create: `portfolio/src/components/MessengerHawk.astro`

- [ ] **Step 1: Write the file**

```astro
---
import SectionHeader from './SectionHeader.astro';
const links = [
  { label: 'Email',    href: 'mailto:jaiganeshharsha@gmail.com', text: 'jaiganeshharsha@gmail.com' },
  { label: 'LinkedIn', href: 'https://www.linkedin.com/in/harsha-jaiganesh/', text: '/in/harsha-jaiganesh' },
  { label: 'GitHub',   href: 'https://github.com/Harsha03', text: '@Harsha03' },
];
---
<section>
  <SectionHeader id="hawk" label="Messenger Hawk" plain="Contact" kanji="手紙" />
  <div class="mx-auto grid max-w-page gap-4 px-6 pb-24 md:grid-cols-3">
    {links.map(l => (
      <a href={l.href} rel="noopener" target="_blank"
         class="border border-tan/20 bg-ink-2 p-6 hover:border-konoha/60 transition-colors">
        <p class="text-xs uppercase tracking-[0.2em] text-tan">{l.label}</p>
        <p class="mt-2 font-serif text-xl text-paper">{l.text}</p>
      </a>
    ))}
  </div>
  <footer class="mx-auto max-w-page px-6 pb-10 text-xs text-tan/60">© {new Date().getFullYear()} Harsha Jaiganesh · 木ノ葉</footer>
</section>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/MessengerHawk.astro
git commit -m "component: MessengerHawk contact section"
```

---

### Task 17: Assemble `index.astro`

**Files:**
- Modify: `portfolio/src/pages/index.astro`

- [ ] **Step 1: Overwrite the file**

```astro
---
import Base from '../layouts/Base.astro';
import Nav from '../components/Nav.astro';
import Hero from '../components/Hero.astro';
import Nindo from '../components/Nindo.astro';
import MissionLog from '../components/MissionLog.astro';
import Missions from '../components/Missions.astro';
import Jutsu from '../components/Jutsu.astro';
import Scroll from '../components/Scroll.astro';
import MessengerHawk from '../components/MessengerHawk.astro';
---
<Base title="Harsha Jaiganesh — Portfolio">
  <Nav />
  <main>
    <Hero />
    <Nindo />
    <MissionLog />
    <Missions />
    <Jutsu />
    <Scroll />
    <MessengerHawk />
  </main>
</Base>
```

- [ ] **Step 2: Build and serve**

```bash
npm run build && npm run preview &
sleep 2
curl -s http://localhost:4321 | grep -c "Harsha Jaiganesh"
kill %1
```

Expected: The curl finds at least 2 occurrences (nav + hero).

- [ ] **Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "pages: compose single-page portfolio"
```

---

### Task 18: Manual smoke + accessibility pass

- [ ] **Step 1: Run dev server and open in browser**

```bash
npm run dev
```

Expected: `http://localhost:4321` loads. Verify manually:

- All nav links scroll to the correct section
- Resume PDF renders inline and the Download button downloads
- All external links open in a new tab
- Mobile viewport (Chrome DevTools 375px) has no horizontal scroll
- With CSS disabled (DevTools → Rendering → Emulate CSS disabled), page still reads as a plain resume
- With JS disabled, page still fully functional

Kill the dev server when done.

- [ ] **Step 2: Lighthouse**

```bash
npm run build
npx serve dist -l 5000 &
sleep 2
npx lighthouse http://localhost:5000 --only-categories=performance,accessibility,best-practices,seo --quiet --chrome-flags="--headless" --output=json --output-path=/tmp/lh.json
node -e "const r=require('/tmp/lh.json').categories; console.log(Object.fromEntries(Object.entries(r).map(([k,v])=>[k,v.score])))"
kill %1
```

Expected: All four scores ≥ 0.95. If any are lower, fix before commit. Common fixes: add missing `lang` attribute, ensure image alts, preconnect fonts.

- [ ] **Step 3: Commit any fixes**

```bash
git add -A
git commit -m "a11y: lighthouse pass, fixes"
```

---

### Task 19: Configure Netlify deployment

**Files:**
- Create: `portfolio/netlify.toml`

- [ ] **Step 1: Write the file**

```toml
[build]
  command = "npm run build"
  publish = "dist"

[[headers]]
  for = "/fonts/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
  for = "/*"
  [headers.values]
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
```

- [ ] **Step 2: Commit**

```bash
git add netlify.toml
git commit -m "deploy: netlify config with font caching + security headers"
```

- [ ] **Step 3: Deploy via Netlify CLI (user-driven)**

Ask the user to run (requires their Netlify login):

```bash
npx netlify-cli deploy --build --prod
```

Expected: Netlify prompts for auth on first run, then returns a public URL like `https://harsha-jaiganesh.netlify.app`. Record the URL in the PR description. If the user prefers GitHub + Netlify integration instead of CLI, skip this step and have them link the repo in the Netlify dashboard.

---

### Task 20: Final review

- [ ] **Step 1: Verify git log is clean and meaningful**

```bash
git log --oneline
```

Expected: ~19 commits telling a coherent story (scaffold → theme → content → components → pages → a11y → deploy).

- [ ] **Step 2: User acceptance**

Share the Netlify URL with the user. They should:

- Open on desktop and mobile
- Download the resume
- Click every project's Source link
- Confirm About copy reads like them (edit `Nindo.astro` if not)
- Pick their preferred tagline if different from the default (edit `Hero.astro` array index)

- [ ] **Step 3: Final commit if edits are needed**

```bash
git add -A
git commit -m "copy: user-approved about + tagline"
```

---

## Self-Review Notes

- Every spec section is covered: Hero (T10), About/Nindo (T11), Mission Log (T12), Missions (T13), Jutsu (T14), Scroll (T15), Messenger Hawk (T16).
- Visual system tokens land in T2.
- Resume handling covered by T3 (copy), T4 (extract), T15 (embed).
- Content schemas prevent shipping broken content (T6).
- Accessibility + Lighthouse gates in T18.
- Deploy is T19; T20 is user sign-off.
- No TBDs. Tagline has three baked-in options; user picks by changing an index.
