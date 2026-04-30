# AGENTS.md — Phanatik (Lexington Themes)

**Phanatik** is a multipage Astro theme for a **newsroom / media-style product**: a homepage with breaking stories, top stories, briefs, and categories; full **blog** and **podcast** experiences with tags, featured lists, and subscriber-only-style routes; plus **jobs**, **help center**, **legal**, **authors**, membership/advertise flows, and auth-style pages (`sign-in`, `sign-up`, `reset`). Primary use case: **publisher or SaaS marketing site** around editorial content and audio.

**Publisher:** [Lexington Themes](https://lexingtonthemes.com/) · **Package:** `@lexington/phanatik` (see `package.json`).

---

## Tech stack

| Area | Details |
|------|---------|
| **Framework** | Astro `^6.0.0` (`package.json`) |
| **Styling** | Tailwind CSS `^4.1.18` via `@tailwindcss/vite` in `astro.config.mjs`; plugins in `src/styles/global.css`: `@tailwindcss/typography`, `@tailwindcss/forms`, `tailwind-scrollbar-hide` |
| **Content** | `@astrojs/mdx` `^5.0.0`; collections in `src/content.config.ts` (Zod via `astro/zod`, `glob` loader) |
| **SEO / feeds** | `@lexingtonthemes/seo` (`AstroSeo` in `src/components/fundations/head/Seo.astro`); `@astrojs/sitemap` `^3.7.0`; `@astrojs/rss` `^4.0.0` |
| **Other deps** | `reading-time` `^1.5.0` (blog posts) |

**`astro.config.mjs` highlights:** `site: 'https://yourdomain.com'` (replace for production); `integrations: [sitemap(), mdx()]`; `markdown.shikiConfig.theme: "github-light"`; top-level `shikiConfig` with `drafts: true`.

---

## Folder map

| Path | Role |
|------|------|
| `src/pages/` | File-based routes (blog, podcast, jobs, help center, legal, authors, system, auth, RSS, etc.) |
| `src/layouts/` | `BaseLayout.astro`, `BlogLayout`, `PodcastLayout`, `LegalLayout`, `JobsLayout`, `HelpCenterLayout`, `AuthorsLayout` |
| `src/components/` | `global/` (Navigation, Footer, Search, …), `fundations/` (head, elements, containers, scripts, icons — **note spelling**), feature folders (`blog/`, `podcast/`, `jobs/`, …), `advert/`, `pricing/`, `assets/` |
| `src/content/` | Markdown (and MDX where allowed) per collection — see below |
| `src/styles/` | `global.css` — Tailwind import, `@theme` tokens (fonts, `accent` / `base` palettes, marquee keyframes), utilities |
| `src/images/` | Blog, podcast, author images; SVG assets under `src/images/assets/` |
| `public/audios/` | Static audio referenced by podcast frontmatter (e.g. `/audios/…`) |

No other tracked files under `public/` besides `public/audios/` in this repo snapshot.

---

## Content collections (`src/content.config.ts`)

Collection **keys** used in code: `legal`, `authors`, `posts`, `podcast`, `jobs`, `helpCenter`.

### `legal`

- **Folder:** `src/content/legal/` (`**/*.md`)
- **Required fields:** `page` (string), `pubDate` (coerced date)
- **Images:** none in schema (inline images in markdown only)
- **Template:** copy from `src/content/legal/terms.md`

### `authors`

- **Folder:** `src/content/authors/` (`**/*.md`)
- **Required fields:** `name` (string), `image: { url, alt }` where `url` uses Astro’s `image()` helper (project-relative paths like `/src/images/authors/8.jpeg` in samples)
- **Optional:** `role`, `bio`, `socials: { twitter?, website?, linkedin?, email? }`
- **Template:** copy from `src/content/authors/samuel-ortiz.md`

### `posts`

- **Folder:** `src/content/posts/` (`**/*.{md,mdx}`)
- **Required fields:** `title`, `pubDate`, `description`, `author` (string slug matching author entry id), `image: { url, alt }`, `tags` (string array)
- **Optional flags:** `isBreaking`, `isTopStory`, `isFeatured`, `isBrief`, `isLocked`
- **Template:** copy from `src/content/posts/1.md`

### `podcast`

- **Folder:** `src/content/podcast/` (`**/*.{md,mdx}`)
- **Required fields:** same core as posts: `title`, `pubDate`, `description`, `author`, `image: { url, alt }`, `tags`
- **Optional:** `episodeNumber`, `duration`, `audioSrc` (e.g. URL under `/audios/`), `isFeatured`, `isGuest`, `isSeries`, `isLocked`
- **Template:** copy from `src/content/podcast/1.md`

### `jobs`

- **Folder:** `src/content/jobs/` (`**/*.md`)
- **Required fields:** `title`, `pubDate`, `jobType`, `company`, `location`, `category`, `jobLevel`, `experience`, `salaryRange`, `description`, `benefits` (string array), `employmentStatus`, `requirements` (string array), `responsibilities` (string array)
- **Optional:** `salaryType`, `referenceId`, `contactEmail`, `hiringManager`, `companyCulture`, `perks`, `skills`, `workEnvironment`, `applicationDeadline`, `mediaLinks`, `applicationInstructions`
- **Template:** copy from `src/content/jobs/1.md`

### `helpCenter`

- **Folder:** `src/content/helpCenter/` (loader base `./src/content/helpCenter` — **camelCase** on disk)
- **Required fields:** `title`, `pubDate`, `description`
- **Images:** none in schema
- **Template:** copy from `src/content/helpCenter/1.md`

---

## Routing conventions

Routes follow `src/pages/` structure. Dynamic detail pages use **rest** params `[...slug].astro` and pass `params.slug` from `entry.id` / `page.id` (the content id derived from the file path/name in the collection).

| Content | Base URL | Detail route file |
|---------|----------|-------------------|
| Blog listing / filters | `/blog`, `/blog/featured`, `/blog/breaking`, `/blog/top-stories`, `/blog/subs-only`, `/blog/tags`, `/blog/tags/[tag]` | — |
| Blog post | `/blog/posts/:slug` | `src/pages/blog/posts/[...slug].astro` |
| Podcast hub | `/podcast`, `/podcast/featured`, `/podcast/guests`, `/podcast/series`, `/podcast/subs-only`, `/podcast/tags`, … | — |
| Episode | `/podcast/episodes/:slug` | `src/pages/podcast/episodes/[...slug].astro` |
| Legal | `/legal/:slug` | `src/pages/legal/[...slug].astro` |
| Jobs | `/jobs`, `/jobs/apply`; listing detail `/jobs/:slug` | `src/pages/jobs/[...slug].astro` |
| Help center | `/helpcenter`, `/helpcenter/:slug` | `src/pages/helpcenter/[...slug].astro` |
| Authors | `/authors`, `/authors/:slug` | `src/pages/authors/[...slug].astro` |
| RSS | `/rss.xml` | `src/pages/rss.xml.js` |
| Design system | `/system/overview`, `/system/colors`, `/system/typography`, `/system/buttons`, `/system/links` | static pages under `src/pages/system/` |

**Note:** `src/pages/rss.xml.js` uses `pagesGlobToRssItems(import.meta.glob('./blog/*.{md,mdx}'))` relative to `src/pages/`. Blog Markdown for the collection lives under `src/content/posts/`, not `src/pages/blog/`. Align the glob with your real markdown source if you rely on RSS for posts.

---

## Customization guide

- **Site URL / canonical domain:** set `site` in `astro.config.mjs` (currently `https://yourdomain.com`). Sitemap and `context.site` (RSS) follow this. Replace placeholders in `src/components/fundations/head/Seo.astro` (`AstroSeo` / meta tags) with your production URLs and handles when you wire per-page SEO.
- **Brand colors & typography:** `src/styles/global.css` — `@theme` block (`--font-sans`, `--font-display`, `--color-accent-*`, `--color-base-*`, marquee animations). `BaseLayout.astro` imports this once.
- **Navigation & footer:** `src/components/global/Navigation.astro`, `src/components/global/Footer.astro` (imported from `src/layouts/BaseLayout.astro`).
- **Document head & global scripts:** `src/components/fundations/head/BaseHead.astro` composes `Seo`, `Meta`, `Fonts`, `Favicons`, and scripts (`KeenSlider`, `ClockScript`, `TickerHideScript`, `NavigationScript`). `BaseLayout.astro` adds sitewide `FuseJS`, `TabsScript`, `AudioPlayerScript`.
- **Path alias:** `@/*` → `src/*` (`tsconfig.json`).

---

## Commands

From `README.md` (run from repo root):

| Command | Action |
|---------|--------|
| `npm install` | Install dependencies |
| `npm run dev` | Dev server |
| `npm run build` | Production build → `./dist/` |
| `npm run preview` | Preview production build |
| `npm run astro ...` | Astro CLI |
| `npm run astro --help` | CLI help |

**Requirements (README):** Node.js 18 or 20 (LTS), npm.

---

## Guardrails

- Do **not** rename the `src/components/fundations/` folder; comments and imports use this spelling throughout.
- Do not widen Zod schemas in `src/content.config.ts` without updating every layout/page that reads `entry.data` / `frontmatter` so types and UI stay consistent.
- Prefer small, pattern-consistent diffs; match existing import style (`@/…`) and layout composition.
- For `image()` fields, keep valid project paths so Astro’s asset handling keeps working.

---

## Lexington Themes links (from README pattern)

Use these as the canonical external references for **this** theme and Lexington:

- **Theme specs:** https://lexingtonthemes.com/templates/phanatik  
- **Documentation:** https://lexingtonthemes.com/documentation  
- **Changelog (theme):** https://lexingtonthemes.com/changelog/phanatik  
- **Support:** https://lexingtonthemes.com/legal/support/  
- **Bundle / purchase:** https://lexingtonthemes.com  

---

## Not present in this repo

- No `changelog` content collection or dedicated changelog route tree (external changelog URL only, per README).
- No environment-specific `.env` documentation in-tree; `package.json` has no runtime env packages.
