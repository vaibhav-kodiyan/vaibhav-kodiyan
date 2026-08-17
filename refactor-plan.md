# Portfolio Site Refactor Recipe

**Goal:** transform an existing personal/portfolio site into one that is simultaneously recruiter-friendly, scraper/ATS-friendly, and visually restrained/corporate. These three audiences want overlapping things — legibility, predictability, real text — so one pass covers all three when done in the right order.

**Order matters.** Do the phases in sequence. Visual polish on top of a JS-only-rendered page is wasted work; metadata on a page with no content hierarchy has nothing to describe.

---

## Phase 0 — Audit (do this first, every time)

Before changing anything, establish a baseline.

```bash
curl -s https://yoursite.com | less        # what does a non-JS client actually see?
curl -s https://yoursite.com/robots.txt
curl -s https://yoursite.com/sitemap.xml
```

- [ ] View page source (not DevTools "Elements" — actual `Ctrl+U` / `view-source:`) and confirm your name, role, and experience text are present as raw HTML
- [ ] Run a Lighthouse audit (Chrome DevTools → Lighthouse) — record SEO, Performance, Accessibility scores
- [ ] Note current stack: static HTML / Next.js / CRA-Vite SPA / Hugo / other — this determines which Phase 1 fix applies

If the audit shows your content **is** present in raw HTML and scores are already reasonable, skip to Phase 2.

---

## Phase 1 — Rendering & Crawlability

*Foundation. Nothing downstream matters if this fails.*

| Step | Action | Done |
|---|---|---|
| 1.1 | If site is client-side-rendered only (blank `<div id="root">` in view-source): migrate to SSG or SSR (Next.js `getStaticProps`/App Router, Astro, or plain static HTML export) | ☐ |
| 1.2 | Add `robots.txt` at site root allowing crawl of all public pages | ☐ |
| 1.3 | Add `sitemap.xml` at site root listing every page | ☐ |
| 1.4 | Re-run Phase 0 curl check — confirm text content now appears without JS execution | ☐ |
| 1.5 | Fix any Lighthouke SEO-flagged blockers (missing lang attribute, no viewport meta, broken links) | ☐ |

**Verification:** `curl -s https://yoursite.com | grep "Your Name"` should return a match.

---

## Phase 2 — Metadata & Structured Data

*Tells machines — search engines, ATS parsers, link-preview bots — who you are and what the page is, explicitly.*

- [ ] `<title>` set per page (not just "Home")
- [ ] `<meta name="description">` per page, human-written, under ~160 characters
- [ ] Open Graph tags: `og:title`, `og:description`, `og:image`, `og:type`
- [ ] Canonical URL tag: `<link rel="canonical" href="...">`
- [ ] JSON-LD `Person` schema in `<head>` of the homepage:

```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Your Name",
  "jobTitle": "Your Title",
  "worksFor": { "@type": "Organization", "name": "Employer" },
  "url": "https://yoursite.com",
  "sameAs": [
    "https://github.com/yourhandle",
    "https://linkedin.com/in/yourhandle"
  ]
}
```

- [ ] Descriptive `alt` text on every image (dual-purpose: SEO + accessibility)

---

## Phase 3 — Content Structure & Curation

*Reorganize so humans skimming and machines parsing get the same clear signal.*

- [ ] Exactly one `<h1>` per page (your name, or the page's purpose)
- [ ] Logical `<h2>`/`<h3>` nesting for sections — Experience, Projects, Skills, Contact
- [ ] Replace generic `<div>` soup with semantic elements: `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>`
- [ ] Nav bar present on every page, same items, same order: **Home / Work / About / Contact**
- [ ] A plain, downloadable resume/CV link (PDF) — placed in the nav, not buried inside "About"
- [ ] Contact info includes at least one non-form channel: a real `mailto:` link or LinkedIn URL
- [ ] Work experience listed in reverse-chronological order, with company name and dates visible without a click
- [ ] Project/experience descriptions curated for outcomes, not duties — e.g. "reduced p99 latency 40% via connection pooling" rather than "worked on backend performance"
- [ ] All body content is real selectable text — no text baked into images

---

## Phase 4 — Visual Restraint ("Corporate Manicured")

*A subtraction pass. Remove until only what supports scanability remains.*

- [ ] Reduce to 1–2 font families total (one for headings is optional; one for body is required)
- [ ] Reduce to 3–4 font sizes across the whole site
- [ ] Pick one accent color; everything else is neutral (black / white / gray scale)
- [ ] Apply a consistent spacing rhythm — an 8px grid is the standard default
- [ ] Remove autoplay video/audio
- [ ] Remove parallax, scroll-jacking, and other motion gimmicks that delay content appearing
- [ ] Confirm dark mode (if present) is optional, not the only mode — some reviewers screenshot or print pages
- [ ] Re-run Lighthouse; Performance score should not have regressed from Phase 0 baseline

---

## Phase 5 — Final Verification

- [ ] `curl -s https://yoursite.com | less` shows full name, role, and experience text
- [ ] `robots.txt` and `sitemap.xml` both resolve
- [ ] Lighthouse SEO score ≥ 95
- [ ] Resume PDF downloads correctly from the nav link
- [ ] Page validates against [W3C HTML validator](https://validator.w3.org/) with no structural errors
- [ ] JSON-LD validates via [Google's Rich Results Test](https://search.google.com/test/rich-results)
- [ ] Manual 5-second skim test: could a stranger state your name, role, and one credential after 5 seconds on the homepage?

---

## Notes

- Stack-specific implementation (exact file paths, framework config) depends on what the site is built in — Next.js, Astro, Hugo, and plain static HTML each solve Phase 1 differently.
- Re-run Phase 0's audit after any redesign — regressions in rendering/crawlability are easy to reintroduce silently (e.g. adding a client-only widget that re-hides content behind JS).